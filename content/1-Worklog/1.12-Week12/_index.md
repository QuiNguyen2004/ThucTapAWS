---
title: "Week 12 Worklog"
date: 2026-07-17
weight: 4
chapter: false
pre: " <b> 1.12. </b> "
---

### Implementation Objectives:

* **Serverless Backend Core API Development:** Provision the `pharmacare-backend-api` AWS Lambda function (Node.js 22.x) running inside a Private Subnet of the VPC, responsible for handling core business logic (product catalogs, store directories, shopping carts, orders, and user profiles) for the **PharmaCare** platform.
* **API Gateway (HTTP API) Deployment:** Establish an Amazon API Gateway HTTP API as the primary client entry point, configuring routing pathways for public/private endpoints and managing CORS policies compatible with the Frontend environment (ReactJS/Vite).
* **API Layer Security via Cognito JWT Authorizer:** Configure the `pharmacare-cognito-authorizer` on API Gateway utilizing JSON Web Tokens (JWT) issued by Amazon Cognito, enforcing a Zero Trust security model on personal data routes (`/profile`, `/cart`, `/orders`).
* **End-to-End (E2E) Integration Testing:** Build a ReactJS (Vite) frontend test suite integrated with OpenID Connect (`react-oidc-context`) to validate the complete communication lifecycle: Client -> API Gateway (JWT Validation) -> Lambda (Secrets Manager) -> RDS PostgreSQL.

### Weekly Task Breakdown:

| Day | Task Description | Start Date | End Date |
| :--- | :--- | :--- | :--- |
| Mon | **Backend Lambda Resource Provisioning:** <br> - Create the `pharmacare-backend-api` Lambda function connected to the Private Subnet and attach its Security Group. <br> - Optimize resources: Memory `256 MB`, Ephemeral storage `512 MB`, Timeout `10 seconds`, and assign the `pharmacare-lambda-role` IAM execution role. | 10/07/2026 | 10/07/2026 |
| Tue | **Source Code Development & Connection Pool Management:** <br> - Initialize a local Node.js project and install required libraries (`pg` and `@aws-sdk/client-secrets-manager`). <br> - Write routing logic in `index.mjs`, configure `RDS_SECRET_ARN` retrieval, and bundle `function.zip` for deployment. | 11/07/2026 | 11/07/2026 |
| Wed | **Amazon API Gateway HTTP API & CORS Configuration:** <br> - Provision the HTTP API and integrate the target with the `pharmacare-backend-api` Lambda function. <br> - Declare public routes (`/health`, `/products`, `/categories`, `/stores`) on the `$default` stage and configure local CORS policies. | 12/07/2026 | 12/07/2026 |
| Thu | **Cognito JWT Authorizer & Protected Route Locking:** <br> - Create `pharmacare-cognito-authorizer` linked to the User Pool and App Client. <br> - Attach the authorizer to sensitive endpoints (`/profile`, `/cart/*`, `/orders/*`) to reject requests lacking valid Bearer Tokens. | 13/07/2026 | 13/07/2026 |
| Fri | **ReactJS Test Interface & E2E Integration Testing:** <br> - Scaffold a ReactJS project using Vite and configure OIDC authentication with a Customer test account. <br> - Execute public/private API test scenarios, inspect JSON responses, and audit system logs in CloudWatch. | 14/07/2026 | 14/07/2026 |

---

### Detailed Implementation Process:

This week, the architectural focus shifted to connecting isolated infrastructure components (VPC, RDS, Cognito) into a unified backend workflow adhering to the modern Serverless API design paradigm:

#### 1. Serverless Lambda Backend Construction & Optimization
* **Lambda Function Provisioning:** Created a new function identified as `pharmacare-backend-api` (Node.js 22.x runtime) deployed within Private App Subnets of the VPC, ensuring complete network isolation from the public internet.

![Backend Lambda Function Overview](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/01-lambda-overview.png)

* **Execution Resource Configuration:** Navigated to General Configuration and upgraded Memory to `256 MB`, Ephemeral storage to `512 MB`, raised Timeout to `10 seconds`, and assigned `pharmacare-lambda-role` to allow sufficient execution window for database connection pooling and secret decryption.

![Memory, Timeout, and IAM Role Configuration](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/02-general-configuration.png)

* **Environment Variable Injection:** Configured environment keys including `DB_NAME` (`pharmacare_ai`) and `RDS_SECRET_ARN`, instructing Lambda to dynamically retrieve master database credentials from AWS Secrets Manager at runtime.

![Environment Variables Configuration for Lambda](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/03-environment-variables.png)

#### 2. Source Code Development & Deployment Packaging
* Initialized a Node.js project workspace in VS Code and installed required packages: `pg` (PostgreSQL client) and `@aws-sdk/client-secrets-manager`.

![Project Initialization and Node.js Dependency Installation](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/04-backend-project-dependencies.png)

* Developed `index.mjs` to parse incoming HTTP methods and routing paths to execute corresponding SQL queries, then compressed the source code and `node_modules` into `function.zip` using PowerShell.

![Packaging Source Code into function.zip](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/05-package-lambda-code.png)

* Uploaded `function.zip` to the AWS Lambda Management Console and clicked **Deploy** to publish the latest backend application logic.

![Backend Source Code After Uploading to Lambda](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/06-upload-lambda-code.png)

#### 3. Amazon API Gateway HTTP API Provisioning
* Accessed the API Gateway Management Console and selected **HTTP API** to minimize latency and optimize operational costs for serverless workloads.

![Selecting Amazon API Gateway HTTP API](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/07-select-http-api.png)

* Configured an Integration target directly linking the HTTP API to the `pharmacare-backend-api` Lambda function.

![Integrating HTTP API with Backend Lambda](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/08-configure-api-integration.png)

* Declared public routing endpoints requiring no authorization: `GET /health`, `GET /products`, `GET /categories`, and `GET /stores`.

![Declaring Public Routes](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/09-configure-public-routes.png)

* Configured the `$default` stage with **Auto-deploy** enabled, ensuring that any route or integration modifications are published immediately without manual intervention.

![Configuring Default Stage for HTTP API](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/10-define-default-stage.png)

![Reviewing Configuration Before API Creation](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/11-review-create-api.png)

* Verified that all defined routing endpoints were successfully created and active within the API dashboard.

![Route List After HTTP API Creation](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/12-api-routes-created.png)

* Implemented a **CORS** policy permitting requests originating from local frontend servers (`http://localhost:5173`) across all standard HTTP methods and the `Authorization` header.

![CORS Configuration for ReactJS](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/13-configure-cors.png)

#### 4. API Layer Security via Cognito JWT Authorizer
* Created a new authorizer of type **JWT**, linking its Identity source (`Authorization` header) to the Issuer URL of the Cognito User Pool and the Audience of the App Client ID.

![Creating Cognito JWT Authorizer](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/14-create-jwt-authorizer.png)

![JWT Authorizer After Creation](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/15-authorizer-created.png)

* Attached the `pharmacare-cognito-authorizer` to sensitive private routes (`/profile`, `/cart`, `/orders`), automatically rejecting unauthorized requests or invalid tokens at the network edge.

![Attaching Cognito Authorizer to GET Profile Route](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/16-attach-authorizer-route.png)

* Tested authentication workflows by logging into the Cognito hosted UI using a test Customer account to verify token issuance and callback redirection.

![Testing Authentication via Cognito Customer Account](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/17-cognito-login-test.png)

#### 5. End-to-End (E2E) Integration Testing with ReactJS Frontend
* Scaffolded a ReactJS application using Vite, installed `react-oidc-context`, and started the local development server.

![Creating ReactJS Application via Vite](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/18-create-react-vite.png)

![ReactJS Application Running on Local Development Server](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/19-react-development-server.png)

* Built a test interface inside `App.jsx` configured to attach the HTTP Header `Authorization: Bearer <access_token>` when calling protected API endpoints.

![ReactJS Test Interface Source Code](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/20-react-test-interface-code.png)

* **E2E Test Execution Results:** The test UI successfully rendered JSON payloads returned from AWS: public APIs responded freely, whereas protected endpoints (`/profile`, `/cart`) only responded successfully when a valid signed JWT Token was included in the request header.

![End-to-End Test Results for Frontend, API Gateway, and Lambda](/ThucTapAWS/images/5-Workshop/5.4-lambda-backend/5.4.1-lambda-backend-api-setup/21-api-test-result.png)

---

### Key Achievements:

* **Secure Serverless Backend Integration:** Successfully integrated the full communication pipeline from Client -> API Gateway -> Lambda -> Secrets Manager -> RDS PostgreSQL.
* **Performance & Zero Trust Security:** Enforced Zero Trust security by validating token claims at the API Gateway edge via Cognito JWT Authorizer while keeping the database instance completely isolated within a Private Subnet.
* **Ready for Frontend & AI Integration:** Core business APIs are operating stably, providing robust endpoints ready to power the production e-commerce user interface and the AI RAG Chatbot module.