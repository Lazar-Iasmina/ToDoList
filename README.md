# AWS Serverless To-Do List

## Introduction
This project is a **serverless To-Do List application** built using **AWS Lambda, API Gateway, and DynamoDB**. The application enables users to **add, view, and delete tasks** dynamically using a REST API. The frontend interacts with this API, making the system fully serverless and scalable.

## Features
- **Serverless architecture** using AWS Lambda.
- **DynamoDB for persistent storage** of tasks.
- **REST API Gateway** to handle HTTP requests.
- **CORS-enabled API** for frontend communication.
- **CRUD operations**: Add, view, and delete tasks dynamically.
- **Auto-generated unique task IDs** via `uuid`.
- **Scalable and cost-effective** with AWS pay-per-use pricing.

---

## Architecture
### Overview
This project follows a **serverless model**, leveraging AWS services for compute, API management, and database storage.
1. **Frontend**: JavaScript-based UI that interacts with API Gateway.
2. **API Gateway**: Routes HTTP requests to AWS Lambda functions.
3. **AWS Lambda**: Processes API requests and interacts with DynamoDB.
4. **DynamoDB**: Stores To-Do tasks with unique `id`s.

### AWS Components
#### 1. **AWS Lambda**
- Handles API requests and executes business logic.
- Auto-scales based on demand.
- Integrates seamlessly with DynamoDB.

#### 2. **Amazon DynamoDB**
- NoSQL key-value database for storing tasks.
- Provides fast, reliable, and scalable storage.
- Uses `id` as the primary key.

#### 3. **Amazon API Gateway**
- Manages HTTP requests from the frontend.
- Routes API requests to Lambda functions.
- Implements security policies such as CORS.

---

## Code Overview

### AWS Lambda Function
The backend is an AWS Lambda function that processes API requests and interacts with DynamoDB.

#### Handling GET Requests (Fetch Tasks)
```python
if method == "GET":
    response = table.scan()
    return {"statusCode": 200, "headers": headers, "body": json.dumps(response['Items'])}
```
- Retrieves all tasks from DynamoDB and returns them as JSON.

#### Handling POST Requests (Add a Task)
```python
if method == "POST":
    data = json.loads(event.get("body", "{}"))
    if "task" not in data:
        return {"statusCode": 400, "body": json.dumps({"error": "Missing task"})}

    item = {"id": str(uuid.uuid4()), "task": data["task"], "status": "pending"}
    table.put_item(Item=item)
    return {"statusCode": 201, "body": json.dumps(item)}
```
- Adds a new task to DynamoDB with a unique `id`.

#### Handling DELETE Requests (Remove a Task)
```python
if method == "DELETE":
    task_id = event.get("queryStringParameters", {}).get("id")
    table.delete_item(Key={"id": task_id})
    return {"statusCode": 200, "body": json.dumps({"message": "Deleted"})}
```
- Deletes a task from DynamoDB using the provided `id`.

---

## Deployment
### Prerequisites
- AWS Account with IAM permissions for Lambda, API Gateway, and DynamoDB.
- AWS CLI or AWS Console access.
- A basic understanding of AWS services.

### Steps
#### 1. Create the DynamoDB Table
- **Table Name**: `ToDoList`
- **Primary Key**: `id` (String)
- **Provisioned Capacity**: On-demand mode recommended.

#### 2. Deploy the Lambda Function
1. Open AWS Lambda and create a new function.
2. Choose **Python** as the runtime.
3. Attach an **IAM Role** with DynamoDB read/write permissions.
4. Copy and paste the `lambda_function.py` code.
5. Deploy the function.

#### 3. Configure API Gateway
1. Create a **new HTTP API** in API Gateway.
2. Define routes:
   - `GET /todo` → Lambda Integration
   - `POST /todo` → Lambda Integration
   - `DELETE /todo` → Lambda Integration
3. Enable **CORS** to allow frontend requests.
4. Deploy the API and note the endpoint URL.

#### 4. Deploy the Frontend
- Update `API_URL` in `index.html` with the deployed API Gateway URL.
- Host the frontend on **Amazon S3 (Static Website Hosting)** or **any web server**.

---

## Running the Application
1. **Open the frontend (`index.html`) in a browser.**
2. **Add, view, and delete tasks dynamically.**
3. **Verify tasks in DynamoDB using AWS Console.**

### Example API Requests
#### Fetch Tasks
```sh
curl -X GET https://your-api-id.execute-api.us-east-1.amazonaws.com/prod/todo
```
#### Add a Task
```sh
curl -X POST https://your-api-id.execute-api.us-east-1.amazonaws.com/prod/todo \
     -H "Content-Type: application/json" \
     -d '{"task": "Finish AWS Project"}'
```
#### Delete a Task
```sh
curl -X DELETE "https://your-api-id.execute-api.us-east-1.amazonaws.com/prod/todo?id=task_id"
```

---

## Notes
- The API supports **CORS** for seamless frontend communication.
- If an error occurs, check AWS CloudWatch logs for debugging.
- Extend functionality by adding **user authentication (AWS Cognito)** or **task completion status updates**.

---

## Conclusion
This project demonstrates a **scalable, serverless To-Do List** using AWS services. With DynamoDB for storage, Lambda for backend processing, and API Gateway for communication, it provides a lightweight yet powerful solution for task management.

🚀 **Happy Coding!**

