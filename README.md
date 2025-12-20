# SERVERLESS ATS – SETUP, DEPLOYMENT, LINKS & TESTING (ONE BLOCK ONLY)

## PROJECT LINKS
VIDEO DEMO:
https://drive.google.com/file/d/1DcG9rU77V9OZRAu6cYVR1HOokzPO5j7z/view

POSTMAN API DOCUMENTATION:
https://documenter.getpostman.com/view/48093520/2sB3dQw9rQ

POSTGRESQL:
https://www.postgresql.org/docs/

AWS SERVICES USED:
https://aws.amazon.com/lambda/
https://aws.amazon.com/api-gateway/
https://aws.amazon.com/cognito/
https://aws.amazon.com/step-functions/
https://aws.amazon.com/rds/

## DATABASE INITIALIZATION
/database/schema.sql
- Run this SQL file on PostgreSQL
- Creates all required tables and constraints

## AMAZON RDS (POSTGRESQL)
- Create PostgreSQL RDS instance
- Open inbound port 5432 for Lambda security group
- Connect using DB client
- Execute schema.sql

## AWS LAMBDA (FOR EACH MICROSERVICE)
cd ATS-Job-Service
npm install
- Zip ONLY folder contents
- Create Lambda (Runtime: Node.js 20.x)
- Upload zip
- Configure environment variables

## REQUIRED ENV VARIABLES
DB_HOST=your-rds-endpoint
DB_USER=postgres
DB_PASSWORD=your_password
DB_NAME=postgres
QUEUE_URL=your_sqs_queue_url
STATE_MACHINE_ARN=your_step_function_arn
SENDER_EMAIL=verified_ses_email

## AWS STEP FUNCTIONS
- Create Standard State Machine
- Chain Lambdas:
  - ATS-State-Updater
  - ATS-Email-Worker
- Grant permissions to invoke Lambdas
- Copy State Machine ARN
- Paste ARN into ATS-Workflow-Trigger Lambda

## API GATEWAY
- Create REST API
- Map routes to Lambdas
- Enable CORS on all routes
- Deploy to stage (prod)

## AMAZON COGNITO
- Create User Pool (email sign-in)
- Create App Client (NO client secret)
- Enable USER_PASSWORD_AUTH
- Attach ATS-User-Sync Lambda to Post Confirmation trigger

## JWT TOKEN GENERATION
aws cognito-idp initiate-auth \
--auth-flow USER_PASSWORD_AUTH \
--client-id YOUR_CLIENT_ID \
--auth-parameters USERNAME=user@example.com,PASSWORD=Password@123

- Copy IdToken
- Paste into Postman Authorization header

## CORE TEST FLOW
- Recruiter creates job
- Candidate applies
- Recruiter advances stage
- Step Function executes successfully
- Email notification sent
- Candidate sees updated status

## SECURITY VALIDATION
- Hiring Manager create job → 403 Forbidden
- Candidate advance stage → 403 Forbidden
- Recruiter full access → ALLOWED

## AUTHOR
J DYNS GOWRISH
