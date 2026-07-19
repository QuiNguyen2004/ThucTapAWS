---
title: "Blog 3"
date: 2026-07-06
weight: 1
chapter: false
pre: " <b>3.3. </b> "
---
# Protecting Sensitive Data with Data Masking in Amazon RDS for Oracle
During my internship and research on AWS Databases, I came across a pretty interesting article about **Data Masking in Amazon RDS for Oracle**. This is a topic I hadn't explored before, so I want to share what I've learned with everyone.

I used to think that setting up a Development or UAT environment was as simple as restoring a Production backup. However, after reading this article, I realized that this approach can expose a lot of sensitive customer data.

**For example:**
* Full name
* Email
* Phone number
* Address
* Other personal information

If this data is moved to a Dev/Test environment without protection measures, it poses significant security risks.

## What is Data Masking?

As I understand it, Data Masking is the process of replacing real data with fictitious data while maintaining the original format, allowing the system to function and be tested normally.

**Example:**

| Original Data | After Masking |
| :--- | :--- |
| Nguyen Van A | Customer001 |
| loc@gmail.com | user001@example.com |
| 0909123456 | 0909XXXXXX |

Thanks to this, developers and testers can still use the data for development or testing without seeing the customers' actual information.

## Operational Workflow

Looking at the diagram above, I understand the workflow as follows:

![Operational Workflow Diagram](/images/Blog/blog3.jpg)

1. **EventBridge Scheduler:** This service automatically triggers the process according to a configured schedule, eliminating the need to run it manually every time data needs to be refreshed.
2. **Restore Snapshot:** The system takes a Snapshot from the Production database and restores it to a temporary RDS Instance to perform the next steps.
3. **AWS Secrets Manager:** Instead of storing the username and password directly in the script, credentials are retrieved from AWS Secrets Manager. This is a much safer approach and an AWS Best Practice.
4. **Perform Data Masking:** Once the database is restored, Systems Manager runs the Masking Script to hide sensitive data.
5. **Create a New Snapshot:** After the Data Masking is complete, the system creates a new Snapshot containing the masked data.
6. **Restore to Dev/UAT Environment:** Finally, the masked Snapshot is restored into a database for Developers or Testers to use.

## What I found interesting

What impressed me the most is that AWS doesn't just show how to mask data but also integrates multiple services to automate the entire workflow.

Some of the services used include:
* Amazon EventBridge Scheduler
* AWS Step Functions
* AWS Systems Manager
* AWS Secrets Manager
* Amazon RDS for Oracle

Each service handles a specific task, making the process automated and significantly reducing manual operations.

## Key Takeaways

After studying this article, I took away a few key points:
* Never use Production data directly for Development or Testing environments.
* Data Masking is a solution that protects sensitive data while ensuring it remains usable for testing.
* AWS Secrets Manager helps manage credentials more securely rather than hardcoding them in source code or scripts.
* Automating workflows with AWS services saves time and minimizes errors.

## Conclusion

This was a relatively new topic for me, and it helped me better understand how AWS supports data protection in real-world systems.

Through this article, I realized that when building a system on the Cloud, data security doesn't stop at access control or encryption; it also requires ensuring that data is handled safely when moved to Development or Testing environments.

---
**Reference:** [AWS Database Blog – Data Masking in Amazon RDS for Oracle](https://aws.amazon.com/blogs/database/)