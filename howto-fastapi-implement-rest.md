# fastapi import FastAPI

Excellent. This next question is one that is **very common for Senior Python/FastAPI positions** at companies like ExxonMobil, JPMorgan, Microsoft, Amazon, and data engineering teams because it tests API design, database knowledge, performance, and architecture.

---

# Senior Python API Interview Prep - Question 2

## Question

> **How would you implement a REST API endpoint that returns a list of users? What considerations would you make for performance and scalability?**

---

# What the Interviewer Is Testing

They want to see whether you think beyond:

```python
SELECT * FROM users
```

A senior engineer should immediately think about:

* Pagination
* Filtering
* Sorting
* Searching
* Database indexing
* Response size
* Serialization
* Caching
* Security
* API versioning
* Rate limiting

---

# Senior Answer

> "For a production API that returns a list of users, I would never return the entire table because the dataset could contain millions of records. I would implement pagination using limit and offset or cursor-based pagination, allow filtering and sorting through query parameters, select only the required columns instead of using `SELECT *`, ensure commonly filtered columns are indexed, and return metadata such as the total count and page information. I would also protect the endpoint with authentication and authorization, validate query parameters, and consider caching frequently requested results."

---

# FastAPI Example

```python
from fastapi import FastAPI, Query

app = FastAPI()

users = [
    {"id": 1, "name": "Alice"},
    {"id": 2, "name": "Bob"},
    {"id": 3, "name": "Charlie"},
    {"id": 4, "name": "David"},
    {"id": 5, "name": "Emma"},
]

@app.get("/users")
def get_users(
    skip: int = Query(0, ge=0),
    limit: int = Query(10, ge=1, le=100)
):
    return users[skip:skip + limit]
```

---

# Example Request

```http
GET /users?skip=0&limit=2
```

Response

```json
[
    {
        "id":1,
        "name":"Alice"
    },
    {
        "id":2,
        "name":"Bob"
    }
]
```

---

# Why Limit the Maximum?

Notice this:

```python
limit: int = Query(10, ge=1, le=100)
```

means

* default = 10
* minimum = 1
* maximum = 100

Without limits someone could request

```
GET /users?limit=1000000
```

which could

* consume excessive memory
* overload the database
* increase response time
* create a denial-of-service risk

---

# Filtering

A senior API should support filtering.

```python
@app.get("/users")
def get_users(
    name: str | None = None
):
    if name:
        return [
            user
            for user in users
            if name.lower() in user["name"].lower()
        ]

    return users
```

Example

```
GET /users?name=ali
```

Response

```json
[
  {
    "id":1,
    "name":"Alice"
  }
]
```

---

# Sorting

Example

```
GET /users?sort=name
```

Implementation

```python
users.sort(key=lambda x: x["name"])
```

Real applications would push sorting to the database:

```sql
SELECT id,name
FROM users
ORDER BY name;
```

---

# Database Optimization

Never write

```sql
SELECT *
FROM users;
```

Instead

```sql
SELECT
    id,
    first_name,
    last_name
FROM users;
```

Why?

* Less network traffic
* Lower memory usage
* Faster serialization
* Better performance

---

# Add Database Indexes

Suppose users search by email.

Without an index

```sql
SELECT *
FROM users
WHERE email='john@gmail.com';
```

The database may scan every row.

With an index

```
INDEX(email)
```

Lookup becomes much faster, especially for large tables.

---

# Return Metadata

Instead of returning only an array:

```json
[
   ...
]
```

A production API often returns:

```json
{
    "page":1,
    "pageSize":25,
    "total":2500,
    "items":[
        ...
    ]
}
```

Benefits

* Clients know how many pages exist.
* Easier UI pagination.
* Better API usability.

---

# Caching

Frequently requested data can be cached.

```
Browser

↓

API

↓

Redis Cache

↓

Database
```

Benefits

* Fewer database queries
* Faster response times
* Better scalability

---

# Security

Protect the endpoint with JWT authentication.

```
Client

↓

JWT Token

↓

API Gateway

↓

FastAPI

↓

Database
```

Also verify authorization—for example, only administrators may retrieve all users.

---

# Error Handling

Bad request

```
GET /users?limit=-1
```

FastAPI automatically returns

```http
422 Unprocessable Entity
```

because

```python
Query(ge=1)
```

enforces the minimum value.

---

# Follow-up Interview Questions

## Q1

**Why shouldn't an API return every record?**

**Answer**

Because production databases can contain millions of rows. Returning all records increases memory usage, network traffic, and response time, and can overwhelm both the API and the client.

---

## Q2

**Offset pagination vs Cursor pagination?**

**Answer**

### Offset Pagination

```sql
LIMIT 20 OFFSET 40
```

Pros

* Simple to implement
* Easy to understand

Cons

* Slower for very large datasets
* Can produce inconsistent results if data changes during paging

---

### Cursor Pagination

```text
GET /users?after=1256
```

Pros

* Better performance on large datasets
* Stable when new records are inserted

Cons

* More complex implementation
* Harder to jump directly to a specific page

A senior engineer should know both approaches and choose based on the use case.

---

## Q3

**Where should filtering happen?**

**Good Answer**

Filtering should be performed in the database whenever possible to reduce the amount of data transferred and processed by the application.

Instead of:

```python
all_users = repository.find_all()
filtered = [u for u in all_users if u["city"] == "Houston"]
```

Prefer:

```sql
SELECT *
FROM users
WHERE city = 'Houston';
```

---

## Q4

**How would you optimize this endpoint if it became slow?**

A strong answer could include:

* Add appropriate database indexes.
* Return only the required columns.
* Implement pagination.
* Cache frequently requested results (for example, with Redis).
* Optimize SQL queries and avoid N+1 query problems.
* Use asynchronous I/O for database calls if supported.
* Monitor latency and database performance with metrics and tracing.
* Scale the service horizontally behind a load balancer if needed.

---

# Senior Interview Prep Notes

When discussing a "list" endpoint, interviewers are evaluating whether you think like someone designing an API for **10 users or 10 million users**. Mentioning concepts such as pagination, filtering, indexing, response models, caching, authentication, authorization, and observability demonstrates that you're designing for production-scale systems rather than just implementing basic CRUD.
