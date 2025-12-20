# Serverless Job Application Tracking System (ATS)

A cloud-native, event-driven **Serverless ATS** designed to manage high-volume recruitment workflows.  
Built with AWS Lambda, API Gateway, Cognito, Step Functions, SQS, SES, and PostgreSQL (RDS).

---

## Demo & Documentation

Video Demo:  
https://drive.google.com/file/d/1DcG9rU77V9OZRAu6cYVR1HOokzPO5j7z/view

API Documentation (Postman):  
https://documenter.getpostman.com/view/48093520/2sB3dQw9rQ

PostgreSQL Reference:  
https://www.postgresql.org/docs/

---

## Architecture Overview

This project follows a fully decoupled microservices architecture backed by event-driven messaging.

- Amazon API Gateway – REST entry point  
- Amazon Cognito – Authentication & JWT  
- AWS Lambda – Stateless compute  
- Amazon RDS (PostgreSQL) – Primary database  
- AWS Step Functions – Application lifecycle workflow  
- Amazon SQS + SES – Async processing & emails  

---

## Microservices

ATS-Job-Service  
ATS-Application-Service  
ATS-User-Sync  
ATS-Workflow-Trigger  
ATS-State-Updater  
ATS-Email-Worker  

---

## Workflow & State Management

Valid States:  
Applied → Screening → Interview → Offer → Hired / Rejected  

All transitions are enforced using AWS Step Functions to prevent invalid state changes.

---

## Role-Based Access Control (RBAC)

RBAC enforced at:
- API Gateway Authorizers  
- Lambda business logic  

Key Rules:
- Recruiter: Full access  
- Candidate: Apply + view own applications  
- Hiring Manager: Read-only access  

---

## Database Schema (PostgreSQL)

- companies  
- users  
- jobs  
- applications  
- application_history  

Schema file location:  
/database/schema.sql  

---

## Folder Structure

/serverless-ats  
├── ATS-Job-Service/  
├── ATS-Application-Service/  
├── ATS-User-Sync/  
├── ATS-Workflow-Trigger/  
├── ATS-State-Updater/  
├── ATS-Email-Worker/  
├── database/  
│ └── schema.sql  
├── ATS_Postman_Collection.json  
└── README.md  

---

## Setup & Installation (All Steps)

### Prerequisites
- Node.js v20.x  
- AWS Account  
- PostgreSQL Client (DBeaver / psql)  
- AWS CLI configured  

---

### Database Setup (Amazon RDS)

1. Create PostgreSQL RDS instance  
2. Allow inbound traffic on port 5432  
3. Connect using PostgreSQL client  
4. Run:  
/database/schema.sql  

---

### Lambda Setup (For Each Microservice)

cd ATS-Job-Service  
npm install  

- Zip only contents (not parent folder)  
- Create Lambda (Node.js 20.x)  
- Upload zip  

---

### Environment Variables (Lambda)

DB_HOST=your-rds-endpoint  
DB_USER=postgres  
DB_PASSWORD=your_password  
DB_NAME=postgres  
QUEUE_URL=your_sqs_queue_url  
STATE_MACHINE_ARN=your_step_function_arn  
SENDER_EMAIL=verified_ses_email  

---

### Step Functions

- Create Standard State Machine  
- Chain:  
  - ATS-State-Updater  
  - ATS-Email-Worker  
- Grant Lambda invoke permissions  
- Copy ARN → set in Workflow Trigger Lambda  

---

### API Gateway

- Create REST API  
- Map routes to Lambdas  
- Enable CORS on all routes  
- Deploy to `prod`  
- Copy Invoke URL  

---

### Amazon Cognito

1. Create User Pool (Email login)  
2. Create App Client (no client secret)  
3. Enable USER_PASSWORD_AUTH  
4. Attach ATS-User-Sync Lambda to Post Confirmation trigger  

---

## How to Test

### Generate JWT Token

aws cognito-idp initiate-auth  
--auth-flow USER_PASSWORD_AUTH  
--client-id YOUR_CLIENT_ID  
--auth-parameters USERNAME=user@example.com,PASSWORD=Example@123  

Copy `IdToken` into Postman Authorization header.

---

### Test Flow

1. Recruiter creates job  
2. Candidate applies  
3. Recruiter advances application stage  
4. Step Function executes successfully  
5. Email sent via SES  
6. Candidate views updated status  

---

### Security Tests

- Hiring Manager creates job → 403 Forbidden  
- Candidate advances stage → 403 Forbidden  
- Recruiter full access → Allowed  

---

## Author

Built by **J DYNS GOWRISH**
