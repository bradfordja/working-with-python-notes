# Senior Python API Interview Test — Question 3

## Question

Create a FastAPI endpoint that accepts a new product.

The API must:

* Accept `name`, `price`, and `quantity`.
* Reject prices less than or equal to zero.
* Reject negative quantities.
* Calculate the product’s total inventory value.
* Return HTTP status `201 Created`.

### Request

```http
POST /products
Content-Type: application/json
```

```json
{
  "name": "Industrial Sensor",
  "price": 125.50,
  "quantity": 10
}
```

---

# Expected Output

```http
201 Created
```

```json
{
  "id": 1,
  "name": "Industrial Sensor",
  "price": 125.5,
  "quantity": 10,
  "inventory_value": 1255.0
}
```

---

# Solution

```python
from fastapi import FastAPI, status
from pydantic import BaseModel, Field

app = FastAPI()


class ProductRequest(BaseModel):
    name: str = Field(min_length=2, max_length=100)
    price: float = Field(gt=0)
    quantity: int = Field(ge=0)


class ProductResponse(ProductRequest):
    id: int
    inventory_value: float


products: list[ProductResponse] = []


@app.post(
    "/products",
    response_model=ProductResponse,
    status_code=status.HTTP_201_CREATED
)
def create_product(product: ProductRequest) -> ProductResponse:
    new_product = ProductResponse(
        id=len(products) + 1,
        name=product.name,
        price=product.price,
        quantity=product.quantity,
        inventory_value=round(product.price * product.quantity, 2)
    )

    products.append(new_product)

    return new_product
```

---

# Code Explanation

## 1. Request model

```python
class ProductRequest(BaseModel):
```

This Pydantic model defines the data the client must send.

```python
name: str = Field(min_length=2, max_length=100)
```

The product name must contain between 2 and 100 characters.

```python
price: float = Field(gt=0)
```

`gt=0` means **greater than zero**.

A price of `0` or `-10` is rejected automatically.

```python
quantity: int = Field(ge=0)
```

`ge=0` means **greater than or equal to zero**.

A quantity of zero is valid, but a negative quantity is rejected.

---

## 2. Response model

```python
class ProductResponse(ProductRequest):
    id: int
    inventory_value: float
```

The response includes all fields from `ProductRequest`, plus:

* Generated product ID
* Calculated inventory value

---

## 3. HTTP status code

```python
status_code=status.HTTP_201_CREATED
```

A successful resource creation should return:

```http
201 Created
```

A normal `200 OK` response would work technically, but `201` is more RESTful for a successful `POST` that creates a resource.

---

## 4. Inventory calculation

```python
inventory_value=round(
    product.price * product.quantity,
    2
)
```

Calculation:

```text
125.50 × 10 = 1255.00
```

The result is rounded to two decimal places.

---

# Invalid Request Test

```json
{
  "name": "Industrial Sensor",
  "price": -25,
  "quantity": 10
}
```

FastAPI returns:

```http
422 Unprocessable Entity
```

The route function is not executed because Pydantic validation fails first.

---

# Interview Question

## Why should request and response models be separated?

### Senior Answer

> I separate request and response models because clients should not control server-generated fields such as IDs, timestamps, audit information, or calculated values. Separate models also provide a clear API contract and prevent internal or sensitive fields from being unintentionally exposed.

For example, the client should not be allowed to submit this:

```json
{
  "id": 5000,
  "inventory_value": 999999
}
```

Those values belong to the server.

---

# Production Improvement

Using this code is acceptable for an interview demonstration:

```python
products.append(new_product)
```

However, an in-memory list is not production-ready. In a real system, the service should save the product through a repository:

```python
@app.post(
    "/products",
    response_model=ProductResponse,
    status_code=status.HTTP_201_CREATED
)
def create_product(
    product: ProductRequest,
    service: ProductService = Depends(get_product_service)
) -> ProductResponse:
    return service.create_product(product)
```

The architecture would be:

```text
FastAPI route
    ↓
Product service
    ↓
Product repository
    ↓
PostgreSQL or another database
```

## Prep Note

In an interview, emphasize that **FastAPI and Pydantic handle structural validation**, while the service layer handles business validation.

For example:

* Pydantic validates that the price is positive.
* The service verifies that the product SKU is unique.
* The repository handles database operations.
* The route translates results into HTTP responses.
