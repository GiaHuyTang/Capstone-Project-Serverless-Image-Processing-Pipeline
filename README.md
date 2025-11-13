# Capstone Project: Serverless Image Processing Pipeline

## 1. Project Objective and Overview

This project implements a fully serverless image processing pipeline on AWS. [cite_start]The application is designed to automatically resize an original image to a thumbnail size upon external request, demonstrating proficiency in integrating multiple core AWS services[cite: 40, 43].

[cite_start]The application workflow is orchestrated by a State Machine and is triggered via an API Gateway endpoint[cite: 52, 53].

## 2. High-Level Architecture

The system's data flow is structured to separate orchestration from compute logic:

1.  **API Gateway** receives an HTTP POST request from the client.
2.  [cite_start]**API Gateway** uses a VTL Mapping to trigger the **Step Functions State Machine**[cite: 17, 19].
3.  [cite_start]The **Step Functions** state machine invokes the **AWS Lambda Function**[cite: 57, 71].
4.  [cite_start]The **Lambda Function** retrieves the original image from **S3 Bucket (Original)**, processes the resize operation, and stores the resulting thumbnail in **S3 Bucket (Resized)**[cite: 50, 51].

***
**(Please insert your Architecture Diagram image here)**
***

## 3. Prerequisites & Configuration Details

### Prerequisites

To successfully deploy and run this application, the following are required:

* An active AWS account.
* **Lambda Layer:** A Lambda Layer containing the native dependencies for image processing (e.g., the **Pillow** library for Python) must be created and attached to the Lambda Function[cite: 10, 11].
* **Source Code:** Lambda Function code (`lambda_function.py`) and the Step Function Definition (`step_function_asl.json`) must be deployed.

### Core Resource Configuration

All resources are configured in the `US East (N. Virginia)` region.

| Resource | Parameter | Value |
| :--- | :--- | :--- |
| **AWS Region** | Region Code | `us-east-1` |
| **S3 Bucket (Original)** | Name | `capstone-original-image` |
| **S3 Bucket (Resized)** | Name | `capstone-resized-image` |
| **Step Function** | State Machine ARN | `arn:aws:states:us-east-1:396608800509:stateMachine:capstoneProject-cloudComputing` |
| **API Endpoint** | Base Invoke URL | `https://0vb4hngj8g.execute-api.us-east-1.amazonaws.com/please-run` |

### API Gateway VTL Mapping (Crucial Integration)

The `Integration Request` for the POST method uses the following Velocity Template Language (VTL) to correctly format the request body into the required Step Function input structure (`stateMachineArn`, `input`, `name`):

```vtl
{
    "stateMachineArn": "arn:aws:states:us-east-1:396608800509:stateMachine:capstoneProject-cloudComputing",
    "input": "{\"source_bucket\": \"capstone-original-image\",\"file_key\": \"$input.path('$.file_key')\"}",
    "name": "$context.requestId"
}
```

## 4. Testing and Verification Instructions
Follow these steps to test the completed application and generate proof for submission:


### Upload Source Data: Upload your test image (e.g., img1.jpg) to the capstone-original-image S3 Bucket.

### Setup API Call (Postman):

Method: POST

URL: Use your deployed Stage Invoke URL.

Headers: Set Content-Type: application/json.

### Send Request Body: Use the following JSON body, ensuring the file_key matches the file uploaded to S3:

```JSON

{
  "file_key": "img1.jpg"
}
```
### Verify Workflow Execution:

Check the AWS Step Functions Console to confirm a new Execution was created and successfully transitions to Succeeded.

Verify the Graph View shows the correct Choice and Success states were executed.


### Verify Output: Check the destination S3 Bucket (capstone-resized-image) to confirm that the resized image file has been successfully stored.

Author: Gia Huy Tang