# REST API

Excellent. Let's structure this like a senior technical interview at companies such as **ExxonMobil, Chevron, JPMorgan, Microsoft, or Amazon**. I'll ask one question at a time, then provide the model answer, discussion points, code sample, and follow-up questions.

---

# Senior Python API Interview Prep - Question 1

## Question

> **Design and implement a REST API endpoint to retrieve a user by ID. What considerations would you make beyond simply querying the database?**

---

# What the Interviewer Is Looking For

A junior developer might answer:

> "I query the database and return the user."

A senior engineer should discuss:

* API design
* Input validation
* Error handling
* Authentication
* Authorization
* Logging
* Performance
* Database efficiency
* Response models
* HTTP status codes
* Security
* Testing
* Observability

---

# Senior Answer

> "When implementing a GET `/users/{id}` endpoint, I think beyond just retrieving the data. First, I validate the user ID to ensure it's the expected format. Next, I authenticate the caller and verify they are authorized to access the requested user. I query the database efficiently, selecting only the fields needed by the client instead of loading the entire entity. If the user doesn't exist, I return a `404 Not Found` rather than a generic error. I log the request with a correlation ID for traceability, but avoid logging sensitive information. I also consider caching frequently accessed data, pagination for collection endpoints, structured error responses, monitoring, and unit and integration tests to ensure reliability."

---

# FastAPI Example

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

users = {
    1: {"id": 1, "name": "Alice"},
    2: {"id": 2, "name": "Bob"}
}

@app.get("/users/{user_id}")
def get_user(user_id: int):
    user = users.get(user_id)

    if user is None:
        raise HTTPException(
            status_code=404,
            detail="User not found"
        )

    return user
```

---

# Example Request

```http
GET /users/1
```

Response

```json
{
  "id": 1,
  "name": "Alice"
}
```

---

# User Does Not Exist

```http
GET /users/100
```

Response

```http
404 Not Found
```

```json
{
    "detail": "User not found"
}
```

---

# Why Use `HTTPException`?

Instead of returning:

```python
return None
```

FastAPI automatically generates the proper HTTP response:

```http
404 Not Found
```

which is the REST standard.

---

# How Would You Improve This for Production?

A senior engineer would identify several enhancements.

### 1. Authentication

```python
current_user = Depends(get_current_user)
```

Verify the caller is authenticated (for example, using JWT or OAuth2).

---

### 2. Authorization

Don't let every authenticated user access every record.

Example:

```python
if current_user.id != user_id:
    raise HTTPException(
        status_code=403,
        detail="Forbidden"
    )
```

---

### 3. Response Model

Avoid exposing database entities directly.

```python
class UserResponse(BaseModel):
    id: int
    name: str
```

```python
@app.get(
    "/users/{user_id}",
    response_model=UserResponse
)
```

This hides sensitive fields such as passwords or internal IDs.

---

### 4. Logging

```python
logger.info(
    "Fetching user",
    extra={"userId": user_id}
)
```

Useful for troubleshooting while avoiding sensitive data.

---

### 5. Correlation ID

In distributed systems, propagate a correlation ID across services.

```
React

↓

API Gateway

↓

User Service

↓

Database

↓

Audit Service
```

Each service logs the same correlation ID so an entire request can be traced end-to-end.

---

### 6. Database Layer

Instead of:

```python
users.get(user_id)
```

Use a repository or service layer:

```python
user = user_service.find_by_id(user_id)
```

This separates business logic from the API layer.

---

### 7. Dependency Injection

```python
@app.get("/users/{user_id}")
def get_user(
    user_id: int,
    service: UserService = Depends(get_user_service)
):
    return service.find_by_id(user_id)
```

Benefits:

* Easier unit testing
* Loose coupling
* Better maintainability

---

### 8. Caching

Frequently requested user profiles could be cached in Redis to reduce database load.

```
Client

↓

Redis Cache

↓

Database
```

---

### 9. Validation

FastAPI automatically validates path parameters.

```python
user_id: int
```

If the client sends:

```
GET /users/abc
```

FastAPI returns:

```http
422 Unprocessable Entity
```

without executing your endpoint logic.

---

# Follow-up Interview Questions

### Q1

**Why return `404` instead of `500`?**

**Answer:**

`404` indicates the requested resource doesn't exist. A `500 Internal Server Error` should only be returned for unexpected server-side failures.

---

### Q2

**Why use a response model?**

**Answer:**

A response model validates and serializes the output, hides sensitive fields, documents the API automatically, and provides a stable contract for clients.

---

### Q3

**Where should business logic live?**

**Answer:**

Business logic belongs in the service layer. Controllers (or FastAPI route handlers) should focus on handling HTTP requests and responses.

---

### Q4

**What HTTP status codes would you commonly use?**

| Code | Meaning               | Example                                 |
| ---- | --------------------- | --------------------------------------- |
| 200  | OK                    | Successful GET                          |
| 201  | Created               | Successful POST                         |
| 204  | No Content            | Successful DELETE with no response body |
| 400  | Bad Request           | Invalid request data                    |
| 401  | Unauthorized          | Authentication required or failed       |
| 403  | Forbidden             | Authenticated but lacks permission      |
| 404  | Not Found             | Resource doesn't exist                  |
| 409  | Conflict              | Duplicate resource or version conflict  |
| 422  | Unprocessable Entity  | Validation error (FastAPI default)      |
| 500  | Internal Server Error | Unexpected server error                 |

---

# Senior Interview Prep Notes

When discussing an API endpoint, avoid stopping at "I query the database and return the result." A senior engineer should naturally talk about:

* API contract and RESTful design
* Validation and input sanitization
* Authentication and authorization
* Proper HTTP status codes
* Service and repository layers
* Error handling and consistent error responses
* Structured logging and correlation IDs
* Performance (caching, selecting only required fields)
* Testing (unit, integration, API)
* Observability (metrics, tracing, monitoring)

These are the topics that distinguish a senior API developer from someone who can only implement CRUD operations.
