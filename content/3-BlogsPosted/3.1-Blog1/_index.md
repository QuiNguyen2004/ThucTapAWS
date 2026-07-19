---

title: "Blog 1"
date: 2026-07-06
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---
# AWS Multi-AZ and Multi-Region: High Availability Solutions for Cloud Systems
Managing permission management in large-scale Kubernetes environments is always a challenging problem for DevOps and Security engineers. How can we ensure the principle of "least privilege" for each microservice without turning the IAM Roles system into an uncontrollable "dumpster fire"?

This article will introduce a definitive solution to the above problem by combining the power of Amazon EKS Pod Identity and the new Session Policies feature, helping you transition from a bulky permission model to a centralized, lean, and fully automated security architecture.

## Part 1: Challenges from the Old Architecture (Traditional IRSA Mechanism)

Before EKS Pod Identity, the standard method to grant AWS permissions to Pods was IAM Roles for Service Accounts (IRSA). Although a significant step forward compared to attaching IAM Roles directly to EC2 Nodes, IRSA still presents major operational barriers as the system scales:

* **IAM Role Proliferation:** To comply with the least privilege principle, each microservice (or group of Pods) requires a distinct IAM Role with fine-tuned policies. In complex microservice architectures with hundreds of services, this number can reach hundreds or even thousands of IAM Roles, creating massive difficulties for management, auditing, and tagging.
* **Cumbersome Manual Operations:** Setting up IRSA requires engineers to manually map Kubernetes Service Accounts to IAM Roles via complex annotations in the manifest files. This process is time-consuming in CI/CD pipelines and prone to configuration errors.
* **Risk of Over-permissioning:** Due to the operational burden of managing too many Roles, development teams often tend to become "lazy" by sharing a few IAM Roles with broader permissions than necessary across multiple services. This creates serious security vulnerabilities.

## Part 2: Three-Layer Architecture Solution with EKS Pod Identity and Session Policies

To address these challenges, Amazon introduced EKS Pod Identity. This solution not only simplifies the authentication flow but also integrates perfectly with Session Policies to apply dynamic security filters at runtime.

This new architecture is divided into 3 distinct layers:

### Layer 1: Identity Layer (K8s Identity Layer)
Here, the system focuses on the application's identity inside Kubernetes. Application Pods do not need to know or declare the IAM Role ARN directly. Instead, they simply identify themselves using a standard Kubernetes Service Account.

The relationship between the Service Account and the IAM Role is centrally managed and synchronized by the EKS Pod Identity Agent—a component running as a DaemonSet on each worker node. This Agent acts as a proxy, handling the entire credential exchange process on behalf of the Pod.
* **Flow:** `[EKS Cluster]` -> `[Pods]` -> `[EKS Pod Identity Agent]`

### Layer 2: Policy & Control Layer (AWS Policy & Control Layer)
This is the "heart" of the new solution. We use Pod Identity Associations to map the Kubernetes Service Account (at Layer 1) to a shared IAM Role (at Layer 2).

The breakthrough lies here: When a Pod sends a request to retrieve credentials, the AWS STS (Security Token Service) is called. The EKS Pod Identity Agent automatically injects a Session Policy into this process.

Session Policy acts as an additional filter. The actual permissions the Pod receives will be the intersection between the base IAM Role and this Session Policy. This means:
* We can use a single shared IAM Role for multiple Pods.
* However, each Pod (via the Session Policy) will only be granted the exact minimum set of permissions required for itself.
* **Flow:** `[AWS STS]` + `[Session Policies (Scope-down JSON)]` -> Generates `[Temporary Credentials]`

### Layer 3: Target Resource Layer
Downstream AWS services such as Amazon S3, Amazon DynamoDB, or Amazon SQS receive requests from the Pod.

At this stage, the requests have been safely and automatically scoped down. These services do not need to know or care about the internal Kubernetes structure. They simply execute the request based on the "Temporary Credentials" restricted by the Session Policy.
* **Flow:** `[Amazon S3]` | `[Amazon DynamoDB]` | `[Amazon SQS]`

## Part 3: Architectural Overview Diagram

To better visualize the data flow and interacting components, here is the architecture diagram of the EKS Pod Identity solution combined with Session Policies:

![EC2 Architecture vs Lambda Architecture](/ThucTapAWS/images/Blog/blog1.jpg)

*Figure 1: Detailed authentication and authorization flow (Source: Based on Amazon EKS operational flow).*

* **Part 1 (Blue):** Initial setup process, IAM Role registration, and Association creation.
* **Part 2 (Steps 3, 4, 4a, 4b):** Authentication and authorization flow at runtime, where the EKS Pod Identity Agent and AWS STS interact to issue Temporary Credentials and apply the Session Policy.
* **Part 3 (Step 5):** The Pod uses the scoped-down credentials to access Amazon S3.

## Part 4: Conclusion

Transitioning from the traditional IRSA model to the EKS Pod Identity architecture combined with Session Policies brings massive and immediate benefits to organizations running Kubernetes:

* **Operational Optimization:** Significantly reduces permission configuration time from hours (for hundreds of Roles) to just a few minutes (requiring only a simple Association configuration).
* **Solving IAM Role Proliferation:** Completely eliminates the burden of managing a large number of IAM Roles. In practical deployments, this solution helps reduce the number of required IAM Roles by up to 80%.
* **Absolute Security Enhancement:** Ensures 100% of Pods strictly adhere to the "Least Privilege" principle thanks to the ability to dynamically scope down permissions via Session Policies, even when sharing a base root IAM Role.

This is a major step forward in modernizing and securing Amazon EKS clusters, helping operational teams focus on product development instead of wrestling with complex permission configurations.

**Reference original article link:** [Amazon EKS Pod Identity: a new way for applications on EKS to obtain IAM credentials | AWS Containers Blog](https://aws.amazon.com/blogs/containers/amazon-eks-pod-identity-a-new-way-for-applications-on-eks-to-obtain-iam-credentials/)