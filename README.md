# AWS Serverless Pet Cuddle-O-Tron 🐾

A fully serverless, event-driven web application built on AWS that enables users to schedule automated pet cuddle email reminders using a custom timer delay.

![Architecture Diagram](<images/ARCHITECTURE-STAGE5.png>)

> **Credits & Acknowledgments:**  
> Based on the advanced serverless demo lab by **Adrian Cantrill** ([learn.cantrill.io](https://learn.cantrill.io)). Starter code and architecture guidance sourced from the official [aws-serverless-pet-cuddle-o-tron](https://github.com/acantril/learn-cantrill-io-labs/tree/master/aws-serverless-pet-cuddle-o-tron) repository. Implemented, debugged, and documented as a hands-on portfolio build.

---

## 🏗️ Architecture Overview

The application uses a decoupled, serverless architecture split across five core AWS tiers:

1. **Static Frontend**: Hosted on **Amazon S3**, communicating with the backend via browser `fetch()` requests.
2. **API Layer**: **Amazon API Gateway** (REST API) exposes a `/petcuddleotron` endpoint with custom CORS preflight (`OPTIONS`) enabled.
3. **API Compute**: **AWS Lambda** (Python) validates incoming web request payloads and triggers state machine execution.
4. **State Machine Orchestration**: **AWS Step Functions** manages execution flow, utilizing a native `Wait` state for custom timing delays.
5. **Notification Compute & Delivery**: A dedicated **Email Lambda** dispatches messages via **Amazon SES** to confirmed email addresses.

---

## 🛠️ AWS Services Used

* **Amazon SES**: Email identity verification and message delivery.
* **AWS Lambda**: Python compute for payload processing, state machine invocation, and SES email dispatch.
* **AWS Step Functions**: State machine workflow orchestration and dynamic timer delays.
* **Amazon API Gateway**: REST API endpoints, CORS handling, and Lambda Proxy Integration.
* **Amazon S3**: Static web hosting for HTML/CSS/JS frontend assets.
* **Amazon CloudWatch**: Centralized logging, execution tracing, and error debugging.
* **AWS IAM**: Least-privilege execution roles for Lambda, Step Functions, and API Gateway.

---

## 🚀 Step-by-Step Implementation

### Stage 1: Configure Simple Email Service (SES)
Verified identity status for both the sender (`FROM_EMAIL_ADDRESS`) and recipient email addresses in Amazon SES to authorize email dispatch before wiring up automation.

![SES Verified Identities](images/Screenshot%202026-08-13%20124728.png)

---

### Stage 2: Implement Email Lambda Function & IAM Security
Developed `email_remainder_lambda` in Python using `boto3` to trigger SES email delivery. Configured custom IAM execution policies to grant explicit permissions for `ses:*`, `sns:*`, and `states:*` actions.

| Email Lambda Code | IAM Execution Policy |
| :---: | :---: |
| ![Email Lambda Code](images/Screenshot%202026-08-13%20133317.png) | ![IAM Permissions](images/Screenshot%202026-08-13%20133516.png) |

---

### Stage 3: Implement & Configure Step Functions State Machine
Built the core `PetCuddleOTron` state machine using Amazon States Language (ASL). Configured a `Wait` state to dynamically pause execution based on the `waitSeconds` input payload before invoking the email Lambda function.

| ASL Workflow Definition | Successful Execution Run |
| :---: | :---: |
| ![ASL Definition](images/Screenshot%202026-08-13%20134016.png) | ![Step Functions Execution](images/Screenshot%202026-08-13%20131647.png) |

---

### Stage 4: API Gateway & Supporting API Lambda
Created a REST API on API Gateway with a `/petcuddleotron` resource supporting `POST` and `OPTIONS` methods. Wrote an API Lambda function to parse incoming JSON payloads, validate inputs, and invoke `sfn.start_execution()`. Enabled full CORS preflight handling across API Gateway and Lambda proxy headers.

![API Gateway Setup](images/Screenshot%202026-08-13%20132835.png)

---

### Stage 5: Static Frontend Application & Testing
Deployed static web interface assets to an Amazon S3 bucket configured for static website hosting. Updated `serverless.js` to send POST requests to the API Gateway Invoke URL and verified end-to-end execution directly through browser interaction.

![Frontend App Success](images/Screenshot%202026-08-13%20134250.png)

---

### Stage 6: Account Cleanup
To prevent unnecessary AWS charges, all project resources were torn down in reverse order of deployment:
* Emptied and deleted S3 static hosting buckets.
* Deleted API Gateway deployments and REST APIs.
* Removed `PetCuddleOTron` Step Functions state machine.
* Deleted `email_remainder_lambda` and API Lambda functions.
* Cleaned up IAM execution roles and customer-managed policies.

---

## 💡 Key Architectural Learnings

* **Cost-Efficient Workflow Orchestration**: Learned to use AWS Step Functions' native `Wait` state to handle custom timing delays instead of running idle sleeping code in Lambda (`time.sleep()`), preventing unnecessary compute charges and creating a truly decoupled, event-driven architecture.
* **Cross-Origin Resource Sharing (CORS)**: Understood how browsers handle cross-domain security and configured API Gateway CORS preflight settings so the static website hosted on S3 can seamlessly communicate with the backend API.
* **Least-Privilege Security with IAM**: Configured dedicated IAM execution roles for each serverless component, ensuring services only hold the explicit permissions required to perform their specific tasks (such as granting SES email sending or Step Functions invocation).
