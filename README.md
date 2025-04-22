# Serverless-File-Sharing-Platform
Personal Project | AWS Cloud | Python

# Description:
Built a serverless file-sharing system that allows users to securely upload and download files using HTTP APIs. Leveraged AWS Lambda, API Gateway, and Amazon S3 to enable scalable file storage and sharing without managing servers. Integrated IAM roles and policies to securely manage access to storage resources.

# Key Features:
-Implemented two AWS Lambda functions for file upload and download

-Integrated API Gateway to expose HTTP endpoints

-Used Amazon S3 as persistent storage for uploaded files

-Configured IAM roles with s3:GetObject and s3:PutObject permissions

-Tested API endpoints using Postman and cURL

# Tech Stack / Tools: 
AWS (Lambda, API Gateway, S3, IAM), Python, Postman, cURL

# Steps

### Step 1: Create an S3 Bucket  
- Bucket Name: `my-filesharing1-bucket-amc`  
- Used to store all uploaded files.

### Step 2: Create Lambda Functions

**UploadFunction**  
- Runtime: Python 3.9 
- IAM Role: Has **S3 Write** permissions (allowing `s3:PutObject` on the bucket)  
- Code: (your Python upload handler)

**DownloadFunction**  
- Runtime: Python 3.9  
- IAM Role: Has **S3 Read** permissions (allowing `s3:GetObject` on the bucket)  
- Code: (your Python download handler)

---

### Step 3: Create API Gateway — `my-filesharing1-api-amc`

- Create resource `/files`  
- Add two methods:  
  - **POST** → Integrated with `UploadFunction`  
  - **GET** → Integrated with `DownloadFunction`

---

### Step 4: Configure API Gateway Methods

**GET Method:**

- Method Request → Enable **Query String Parameter Validation** for `fileName`  
- Integration Request → Mapping Template for `application/json`:

```json
{
  "queryStringParameters": {
    "fileName": "$input.params('fileName')"
  }
}
```

---

**POST Method:**

- Integration Request → Mapping Template for `text/plain` content-type:

```json
{
  "body": "$input.body",
  "queryStringParameters": {
    "fileName": "$input.params('fileName')"
  }
}
```

---

### Step 5: Deploy API

- Deploy to stage: `dev`  
- URL example:  
  ```
  https://<api-id>.execute-api.<region>.amazonaws.com/dev/files
  ```

---

### Step 6: Testing Your API

**Upload file (POST):**

Using Postman or curl:

```bash
curl --location 'https://<api-id>.execute-api.<region>.amazonaws.com/dev/files?fileName=test.txt' \
--header 'Content-Type: text/plain' \
--data 'Hello World from Satheeshna!'
```

---

**Download file (GET):**

Using Postman or curl:

```bash
curl --location 'https://<api-id>.execute-api.<region>.amazonaws.com/dev/files?fileName=test.txt'
```

---
**Note:** This is a public test endpoint for demonstration purposes only.

### Step 7: Required IAM Role Permissions

**UploadFunction Role Policy (S3 Write permission):**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::my-file-sharing-bucket-amc/*"
    }
  ]
}
```

**DownloadFunction Role Policy (S3 Read permission):**

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::my-file-sharing-bucket-amc/*"
    }
  ]
}
```


