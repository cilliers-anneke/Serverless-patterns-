# AWS Serverless Workshop Notes

## Module 1: Manual Architecture
* **API Gateway:** Acts as the front entry point, exposing the `/users` resource path.
* **Lambda:** Event-driven compute function (`get-users`) written in Python. 
* **DynamoDB:** Fully managed NoSQL key-value database table.

## The Compilation Order (Crucial for Exam)
1. **DynamoDB Table** (Target layer must exist first)
2. **IAM Policy & Execution Role** (Establish security clearance boundaries)
3. **Lambda Function** (Attach the execution role to the code container)
4. **API Gateway** (Expose the execution path to the web via Invoke URL)
