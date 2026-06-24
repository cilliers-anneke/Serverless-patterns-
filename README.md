# AWS Serverless Workshop Notes

Serverless Patterns
Your project...

Getting Started
Serverless patterns

M1 - Intro to Serverless

M2 - Synchronous Invocation

SAM + Python
Module Setup
1 - Create data store
2 - Add Business Logic
Optional - Test locally
3 - Connect an API
3.1 - Create User Pool
3.2 - Secure the API
3.3 - Verify Authorization
4 - Unit Test
5 - Integration Test
6 - Observe the App
6.1 - Set Alarms
6.2 - Display a Dashboard
Clean up

CDK + TypeScript
Module Setup
1 - Create data store
2 - Add Business Logic
Optional - Test locally
3 - Connect an API
3.1 - Create User Pool
3.2 - Secure the API
3.3 - Verify Authorization
4 - Unit Test
5 - Integration Test
6 - Observe the App
6.1 - Set Alarms
6.2 - Display a Dashboard
Clean up

Terraform + Python
Module Setup
1 - Create data store
2 - Add Business Logic
3 - Connect an API
3.1 - Create User Pool
3.2 - Secure the API
3.3 - Verify Authorization
4 - Unit Test
5 - Integration Test
6 - Observe the App
6.1 - Set Alarms
6.2 - Display a Dashboard
Module Cleanup

Terraform + TypeScript
Module Setup
1 - Create data store
2 - Add Business Logic
3 - Connect an API
3.1 - Create User Pool
3.2 - Secure the API
3.3 - Verify Authorization
4 - Unit Test
5 - Integration Test
6 - Observe the App
6.1 - Set Alarms
6.2 - Display a Dashboard
Module Cleanup

M3 - Synchronous + Idempotence

SAM + Python

M4 - Asynchronous Invocation

SAM + Python

M5 - Polling

SAM + Python

M6 - Streaming Data

SAM + Python

Dive Deeper

AWS Infrastructure Composer

CI/CD

Deployment Strategies
Streaming Analytics

Streaming Datalake
Release history

## Module 1: Manual Architecture
* **API Gateway:** Acts as the front entry point, exposing the `/users` resource path.
* **Lambda:** Event-driven compute function (`get-users`) written in Python. 
* **DynamoDB:** Fully managed NoSQL key-value database table.

## The Compilation Order (Crucial for Exam)
1. **DynamoDB Table** (Target layer must exist first)
2. **IAM Policy & Execution Role** (Establish security clearance boundaries)
3. **Lambda Function** (Attach the execution role to the code container)
4. **API Gateway** (Expose the execution path to the web via Invoke URL)

5. The part of this workshop was a manual configuration of DynamoDB, API and Lambda, right? The second is another way to achieve the same result, inserting code into CloudShell or another JSON reading frame, like x, y, z, right? And the third and fourth parts of this workshop, too, right? Four ways to connect these microservices, right? What are these different ways 

You have hit on the exact core architectural concept of this entire AWS training! You are completely right about what is happening behind the scenes, but let's clear up the terminology so it clicks perfectly for your Solutions Architect exam prep.

This workshop isn't showing you four different ways to *connect* microservices; it is showing you **different tools and deployment methodologies to provision and manage** the exact same serverless architecture.

No matter which step you are on, the target architecture remains identical:

Here is the breakdown of the different approaches you are exploring across these workshop modules:

---

### 1️⃣ Approach 1: Manual Provisioning (The "ClickOps" Way)

* **What you did:** In Module 1 ("Intro to Serverless"), you went into the AWS Management Console and manually created the DynamoDB table, wrote the Lambda function in the browser console editor, and manually configured API Gateway routes.
* **The Purpose:** To help you understand the foundational basics. You learn exactly what settings, IAM roles, and permissions are required to make these services talk to each other by clicking and stitching them together by hand.
* **The Verdict:** Great for learning; terrible for production because it is prone to human error and cannot be automated.

---

### 2️⃣ Approach 2: Infrastructure as Code (IaC) via AWS SAM

* **What you are doing now:** In Module 2 ("Synchronous Invocation"), instead of clicking around the console, you are defining your infrastructure as a flat text blueprint inside a file (`template.yaml`).
* **How it works:** When you run `sam build` and `sam deploy`, AWS reads that text file, converts it into an AWS CloudFormation stack template, and provisions everything automatically in your account.
* **The Purpose:** This introduces you to **DevOps principles**. You can version-control your infrastructure in GitHub, deploy identical copies of your app instantly, and completely avoid the configuration drift that happens with manual clicking.

---

### 3️⃣ Approach 3: Local Simulation & Testing (`sam local`)

* **What you just set up:** This is the part where you use JSON text files (`env.json` and `event-post-user.json`) to trick a local mock environment into thinking it's running live in the cloud.
* **How it works:** `sam local invoke` reads those JSON payloads via CloudShell, spins up a local Docker container that mimics AWS Lambda execution environment, injects your variables, and executes your Python script.
* **The Purpose:** It teaches you how to test code changes locally on your laptop or CloudShell without waiting several minutes to deploy changes to actual AWS cloud servers every time you modify a line of code.

---

### 4️⃣ Approach 4: Production Integration & Authorization (The Rest of Module 2)

* **What is coming up next:** If you look closely at your workshop navigation sidebar in `image_4362bb.jpg`, the subsequent steps are "3 - Connect an API", "3.1 - Create User Pool", and "3.2 - Secure the API".
* **How it works:** This phase moves you away from testing simulated JSON strings. You will wire up an actual, live Amazon API Gateway REST API endpoint linked to an Amazon Cognito User Pool for enterprise security.
* **The Purpose:** To show you how to take a working backend and transform it into a secure, scalable, real-world internet application that requires authenticating users before they can touch your database.

---

### 🎓 Solutions Architect Study Tip

Keep this distinction in mind for your upcoming exam:

* **DynamoDB, Lambda, and API Gateway** are your architectural components (the building blocks).
* **AWS SAM and CloudFormation** are your deployment tools (the construction crew).

Did your local test invocation from the previous step finish executing successfully?

Difference between synchronous and asynchronous invocation

For your Solutions Architect exam, understanding the difference between **Synchronous** and **Asynchronous** lambda invocations is a non-negotiable core concept. It dictates how your services handle traffic, manage failures, and scale.

Here is the breakdown of how they work, how they behave, and exactly when to use each pattern.

---

## ⚡ Synchronous Invocation (The "Wait for It" Pattern)

In a synchronous invocation, the client application triggers the Lambda function and **holds the connection open**, waiting for the function to finish executing and return a response.

* **The Workflow:** Client Request $\rightarrow$ Lambda Executes $\rightarrow$ Immediate Response returned to Client.
* **Error Handling:** If the function fails or times out, the error is returned to the client *immediately*. It is up to the client application to catch the error and decide whether to try again.
* **Common Triggering Services:**
* **Amazon API Gateway / Application Load Balancer** (like the web endpoint you are building in this workshop module).
* **AWS AppSync** (GraphQL APIs).
* **Amazon Cognito** (Triggers checking a user's password during login).



---

## ⏳ Asynchronous Invocation (The "Fire and Forget" Pattern)

In an asynchronous invocation, the client triggers the Lambda function and **walks away immediately**. The client does not wait for the code to run; it only cares that AWS successfully received the event.

* **The Workflow:** Client Sends Event $\rightarrow$ AWS drops it into an internal, hidden event queue $\rightarrow$ AWS returns a `202 Accepted` status to the client $\rightarrow$ Lambda processes the event out of the queue in the background.
* **Error Handling:** Because the client is already gone, Lambda handles retries automatically. By default, it will automatically retry a failing event **two more times** (3 total attempts). If it continues to fail, you can route the dead event to a **DLQ (Dead Letter Queue)** or an **AWS Lambda Destination** (like an SQS queue or SNS topic) for troubleshooting.
* **Common Triggering Services:**
* **Amazon S3** (e.g., a user uploads a photo, S3 kicks off a background task to resize it).
* **Amazon SNS** (Publish/Subscribe notifications).
* **Amazon EventBridge** (Scheduled cron jobs or system alerts).



---

## 📊 Quick Reference Comparison

| Feature | Synchronous Invocation | Asynchronous Invocation |
| --- | --- | --- |
| **Client Behavior** | Waits for the response. | Fires and forgets immediately. |
| **Immediate Response** | The function's output or error. | `202 Accepted` (confirming receipt). |
| **Built-in Retries?** | No. The client must handle retries. | Yes. Automatically retries twice. |
| **Failure Handling** | Client handles it. | Routes to Dead Letter Queues (DLQ) / Destinations. |
| **Best Used For** | Real-time web APIs, interactive apps. | Heavy background processing, decoupling services. |

---

## 🎯 Exam Scenario Cheat Sheet

* **Scenario A:** *"You are building an e-commerce checkout page where the user needs an instant confirmation invoice screen."* * **Answer:** **Synchronous** (via API Gateway to Lambda). The user cannot proceed without seeing the result.
* **Scenario B:** *"An application needs to process PDF bank statements uploaded by users. The processing takes 4 minutes, and the user doesn't need to sit and stare at a loading screen."* * **Answer:** **Asynchronous** (via S3 Event Notification). Drop the file, return a "We are processing your file" message, and let Lambda grind through it in the background.

Since you are currently working on **Module 2: Synchronous Invocation**, your REST API fits right into Scenario A!

How is your local container test coming along—did the mock POST event successfully insert that user into your table?
