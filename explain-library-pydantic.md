# Pydantic in Python

**Pydantic is a Python library for validating, converting, and serializing data using Python type hints.**

It is commonly used with:

* FastAPI request and response models
* JSON validation
* Configuration data
* Database DTOs
* External API payloads
* Environment settings

A Pydantic model is a class that inherits from `BaseModel`. Pydantic checks incoming data and creates an object whose values match the declared types and constraints. ([Pydantic Docs][1])

---

## Basic Example

```python
from pydantic import BaseModel, Field


class Product(BaseModel):
    id: int
    name: str = Field(min_length=2, max_length=100)
    price: float = Field(gt=0)
    quantity: int = Field(ge=0, default=0)


product = Product(
    id="101",
    name="Laptop",
    price=1299.99
)

print(product)
```

Output:

```text
id=101 name='Laptop' price=1299.99 quantity=0
```

Notice that:

```python
id="101"
```

was provided as a string, but Pydantic converted it to:

```python
id=101
```

an integer.

By default, Pydantic may coerce compatible values into the declared type. Strict mode can be used when automatic conversion is not desired. ([Pydantic Docs][1])

---

# Why Use Pydantic?

Without Pydantic, you may need to validate data manually:

```python
def create_product(data):
    if "name" not in data:
        raise ValueError("Name is required")

    if not isinstance(data["price"], (int, float)):
        raise TypeError("Price must be numeric")

    if data["price"] <= 0:
        raise ValueError("Price must be greater than zero")
```

With Pydantic:

```python
class ProductRequest(BaseModel):
    name: str
    price: float = Field(gt=0)
```

Pydantic handles:

* Required fields
* Type validation
* Type conversion
* Numeric constraints
* String constraints
* Nested objects
* Error messages
* JSON conversion
* JSON Schema generation

---

# 1. `BaseModel`

`BaseModel` is the parent class for most Pydantic models.

```python
from pydantic import BaseModel


class Employee(BaseModel):
    id: int
    name: str
    salary: float
```

Create an object:

```python
employee = Employee(
    id=100,
    name="Alice",
    salary=95000
)
```

Fields are normally defined with Python type hints:

```python
id: int
name: str
salary: float
```

---

# 2. `Field()`

`Field()` adds validation rules, aliases, defaults, and documentation metadata to a model field.

```python
from pydantic import BaseModel, Field


class User(BaseModel):
    username: str = Field(
        min_length=3,
        max_length=30
    )

    age: int = Field(
        ge=18,
        le=120
    )

    salary: float = Field(
        gt=0
    )
```

## Common constraints

| Constraint      | Meaning                     |
| --------------- | --------------------------- |
| `gt=0`          | Greater than 0              |
| `ge=0`          | Greater than or equal to 0  |
| `lt=100`        | Less than 100               |
| `le=100`        | Less than or equal to 100   |
| `min_length=3`  | Minimum string length       |
| `max_length=50` | Maximum string length       |
| `pattern=...`   | Regular-expression pattern  |
| `default=...`   | Default value               |
| `alias=...`     | Alternate input/output name |

Pydantic supports field customization through `Field()`, including constraints and schema metadata. ([Pydantic Docs][2])

---

# 3. Creating a Model Normally

The most common way to validate data is through the constructor:

```python
product = Product(
    id=1,
    name="Keyboard",
    price=75.50,
    quantity=10
)
```

Pydantic automatically validates the values during model creation.

Invalid data raises `ValidationError`:

```python
from pydantic import ValidationError

try:
    product = Product(
        id=1,
        name="K",
        price=-25,
        quantity=-1
    )

except ValidationError as error:
    print(error)
```

---

# Core Pydantic Model Methods

## 4. `model_validate()`

`model_validate()` validates a Python object, usually a dictionary, against a model.

```python
data = {
    "id": "101",
    "name": "Monitor",
    "price": "299.99",
    "quantity": 5
}

product = Product.model_validate(data)

print(product)
```

Output:

```text
id=101 name='Monitor' price=299.99 quantity=5
```

Use it when your data comes from:

* A dictionary
* A database result
* A message queue
* Another Python object
* An external service

```python
product = Product.model_validate(data)
```

The method validates the object and returns an instance of the model. ([Pydantic Docs][1])

---

## 5. `model_validate_json()`

Validates a JSON string or JSON bytes directly.

```python
json_data = """
{
    "id": 101,
    "name": "Monitor",
    "price": 299.99,
    "quantity": 5
}
"""

product = Product.model_validate_json(json_data)

print(product.name)
```

Output:

```text
Monitor
```

This is preferred when the original input is already JSON:

```python
Product.model_validate_json(json_data)
```

It avoids manually doing:

```python
import json

data = json.loads(json_data)
product = Product.model_validate(data)
```

Pydantic documents `model_validate_json()` as the method for validating JSON strings or bytes directly. ([Pydantic Docs][1])

---

## 6. `model_validate_strings()`

This validates dictionaries whose keys and values are strings.

```python
data = {
    "id": "101",
    "name": "Laptop",
    "price": "1299.99",
    "quantity": "5"
}

product = Product.model_validate_strings(data)

print(product)
```

Output:

```text
id=101 name='Laptop' price=1299.99 quantity=5
```

This can be useful with:

* HTML form data
* Query parameters
* Environment variables
* CSV data
* Command-line input

---

## 7. `model_dump()`

Converts a Pydantic model into a Python dictionary.

```python
product = Product(
    id=101,
    name="Laptop",
    price=1299.99,
    quantity=5
)

data = product.model_dump()

print(data)
```

Output:

```python
{
    "id": 101,
    "name": "Laptop",
    "price": 1299.99,
    "quantity": 5
}
```

This is useful when:

* Saving data to a database
* Passing data to another service
* Creating an API response
* Logging model values
* Updating another object

Pydantic recommends `model_dump()` for recursively converting model data into a dictionary. ([Pydantic Docs][1])

### Excluding fields

```python
data = product.model_dump(
    exclude={"quantity"}
)
```

### Including selected fields

```python
data = product.model_dump(
    include={"id", "name"}
)
```

### Excluding unset values

```python
data = product.model_dump(
    exclude_unset=True
)
```

This is useful for PATCH operations.

---

## 8. `model_dump_json()`

Converts a Pydantic model to a JSON string.

```python
json_result = product.model_dump_json()

print(json_result)
```

Output:

```json
{"id":101,"name":"Laptop","price":1299.99,"quantity":5}
```

Difference:

```python
product.model_dump()
```

returns a dictionary.

```python
product.model_dump_json()
```

returns a JSON string.

Serialization methods support options for controlling which values and fields are included. ([Pydantic Docs][3])

---

## 9. `model_copy()`

Creates a copy of a model.

```python
original = Product(
    id=101,
    name="Laptop",
    price=1299.99,
    quantity=5
)

copy = original.model_copy()

print(copy)
```

You can copy and update fields:

```python
updated = original.model_copy(
    update={
        "price": 1199.99,
        "quantity": 10
    }
)

print(updated)
```

Output:

```text
id=101 name='Laptop' price=1199.99 quantity=10
```

By default, `model_copy()` creates a shallow copy. ([Pydantic Docs][1])

---

## 10. `model_json_schema()`

Generates the JSON Schema for the model.

```python
schema = Product.model_json_schema()

print(schema)
```

Simplified result:

```python
{
    "title": "Product",
    "type": "object",
    "properties": {
        "id": {
            "title": "Id",
            "type": "integer"
        },
        "name": {
            "title": "Name",
            "type": "string",
            "minLength": 2,
            "maxLength": 100
        },
        "price": {
            "title": "Price",
            "type": "number",
            "exclusiveMinimum": 0
        }
    }
}
```

FastAPI uses generated schemas to create:

* OpenAPI definitions
* Swagger documentation
* Request schemas
* Response schemas

---

## 11. `model_construct()`

Creates a model **without validation**.

```python
product = Product.model_construct(
    id="not-an-integer",
    name="X",
    price=-500,
    quantity=-10
)

print(product)
```

This succeeds because validation was skipped.

Use it only when:

* The data has already been validated
* The source is completely trusted
* Validators must not run
* Profiling proves it is useful

Pydantic warns that `model_construct()` can create invalid model instances and should only be used with trusted or previously validated data. ([Pydantic Docs][1])

---

# Custom Validation

## 12. `@field_validator`

Validates or transforms one specific field.

```python
from pydantic import BaseModel, field_validator


class Employee(BaseModel):
    name: str
    email: str
    salary: float

    @field_validator("email")
    @classmethod
    def validate_email(cls, value: str) -> str:
        if "@" not in value:
            raise ValueError(
                "Email must contain @"
            )

        return value.lower()
```

Usage:

```python
employee = Employee(
    name="Alice",
    email="ALICE@EXAMPLE.COM",
    salary=95000
)

print(employee.email)
```

Output:

```text
alice@example.com
```

A validator must return the validated or transformed value:

```python
return value
```

Field validators may run before or after normal Pydantic validation, depending on their configured mode. ([Pydantic Docs][4])

---

## Validator modes

```python
@field_validator("field_name", mode="before")
```

### `before`

Runs before normal type conversion.

```python
class TagsRequest(BaseModel):
    tags: list[str]

    @field_validator("tags", mode="before")
    @classmethod
    def convert_string_to_list(cls, value):
        if isinstance(value, str):
            return value.split(",")

        return value
```

Input:

```python
TagsRequest(tags="python,fastapi,pydantic")
```

Result:

```python
["python", "fastapi", "pydantic"]
```

Other modes include:

* `before`
* `after`
* `plain`
* `wrap`

For most business validation, `before` and `after` are the commonly discussed modes.

---

## 13. `@model_validator`

Validates multiple fields or the entire model.

```python
from pydantic import BaseModel, model_validator


class DateRange(BaseModel):
    start_date: int
    end_date: int

    @model_validator(mode="after")
    def validate_date_range(self):
        if self.end_date < self.start_date:
            raise ValueError(
                "End date must be after start date"
            )

        return self
```

Usage:

```python
date_range = DateRange(
    start_date=10,
    end_date=20
)
```

Invalid example:

```python
DateRange(
    start_date=20,
    end_date=10
)
```

This raises a validation error.

Use `model_validator` when validation depends on more than one field:

* Start date must precede end date
* Password and confirmation must match
* Discount cannot exceed price
* Either email or phone must be provided
* One field is required based on another field

---

# Handling Validation Errors

## 14. `ValidationError`

Pydantic raises `ValidationError` when input cannot be converted into a valid model.

```python
from pydantic import ValidationError

try:
    product = Product(
        id="abc",
        name="A",
        price=-10,
        quantity=-5
    )

except ValidationError as error:
    print(error)
```

You can retrieve structured errors:

```python
except ValidationError as error:
    print(error.errors())
```

Example:

```python
[
    {
        "type": "int_parsing",
        "loc": ("id",),
        "msg": "Input should be a valid integer",
        "input": "abc"
    }
]
```

Useful methods include:

```python
error.errors()
error.json()
error.error_count()
```

Pydantic collects validation problems into a single `ValidationError`, allowing applications to inspect structured error details. ([Pydantic Docs][5])

---

# Nested Models

Pydantic supports models inside other models.

```python
from pydantic import BaseModel


class Address(BaseModel):
    street: str
    city: str
    state: str
    zip_code: str


class Customer(BaseModel):
    id: int
    name: str
    address: Address
```

Create the parent object from nested dictionaries:

```python
customer = Customer(
    id=1,
    name="Alice",
    address={
        "street": "100 Main Street",
        "city": "Houston",
        "state": "TX",
        "zip_code": "77002"
    }
)
```

Pydantic automatically converts the dictionary into an `Address` model:

```python
print(type(customer.address))
```

Output:

```text
<class '__main__.Address'>
```

Nested models are recursively validated and serialized. ([Pydantic Docs][1])

---

# Optional Fields and Defaults

```python
class User(BaseModel):
    id: int
    name: str
    phone: str | None = None
    active: bool = True
```

Important difference:

```python
phone: str | None
```

means the field can contain `None`, but it is still required unless a default is supplied.

This makes it optional:

```python
phone: str | None = None
```

---

# Lists and Collections

```python
class Order(BaseModel):
    order_id: int
    product_ids: list[int]
    tags: set[str] = set()
```

Input:

```python
order = Order(
    order_id=100,
    product_ids=["1", "2", "3"],
    tags=["priority", "online", "priority"]
)
```

Result:

```python
print(order.product_ids)
# [1, 2, 3]

print(order.tags)
# {"priority", "online"}
```

Pydantic validates items inside the collections as well as the collection itself.

For mutable defaults, a clearer production pattern is:

```python
from pydantic import Field

tags: set[str] = Field(default_factory=set)
```

---

# Configuration with `ConfigDict`

Use `ConfigDict` to control model-level behavior.

```python
from pydantic import BaseModel, ConfigDict


class Product(BaseModel):
    model_config = ConfigDict(
        extra="forbid",
        validate_assignment=True
    )

    id: int
    name: str
    price: float
```

## `extra="forbid"`

Rejects unexpected fields.

```python
Product(
    id=1,
    name="Laptop",
    price=1000,
    unknown_field="invalid"
)
```

This raises `ValidationError`.

Possible extra-field policies are:

* `ignore` — ignore extra fields; default behavior
* `forbid` — reject extra fields
* `allow` — preserve extra fields

These behaviors are controlled through model configuration. ([Pydantic Docs][1])

## `validate_assignment=True`

Normally, models are validated when created, but direct attribute updates may not be revalidated.

With assignment validation:

```python
product.price = "invalid"
```

raises a validation error.

---

# Aliases

Aliases let JSON field names differ from Python property names.

```python
from pydantic import BaseModel, Field


class User(BaseModel):
    first_name: str = Field(
        alias="firstName"
    )
```

Input:

```python
user = User(
    firstName="Alice"
)
```

Access in Python:

```python
print(user.first_name)
```

Serialize using the alias:

```python
print(
    user.model_dump(by_alias=True)
)
```

Output:

```python
{
    "firstName": "Alice"
}
```

This is especially useful when working with JavaScript APIs that use camelCase.

---

# Pydantic with FastAPI

FastAPI uses Pydantic models for request validation and response serialization.

```python
from fastapi import FastAPI
from pydantic import BaseModel, Field

app = FastAPI()


class ProductRequest(BaseModel):
    name: str = Field(
        min_length=2,
        max_length=100
    )

    price: float = Field(gt=0)

    quantity: int = Field(
        ge=0,
        default=0
    )


class ProductResponse(BaseModel):
    id: int
    name: str
    price: float
    quantity: int


@app.post(
    "/products",
    response_model=ProductResponse,
    status_code=201
)
async def create_product(
    request: ProductRequest
) -> ProductResponse:
    return ProductResponse(
        id=101,
        **request.model_dump()
    )
```

Request:

```json
{
    "name": "Laptop",
    "price": 1299.99,
    "quantity": 5
}
```

FastAPI and Pydantic perform this flow:

```text
HTTP JSON
   ↓
Pydantic model
   ↓
Type conversion
   ↓
Field validation
   ↓
Custom validators
   ↓
FastAPI endpoint
   ↓
Response model serialization
```

---

# Core Methods Cheat Sheet

| Method or feature          | Purpose                                          |
| -------------------------- | ------------------------------------------------ |
| `BaseModel`                | Base class for validated models                  |
| `Field()`                  | Adds constraints, defaults, aliases and metadata |
| `model_validate()`         | Validates a Python dictionary or object          |
| `model_validate_json()`    | Validates JSON text or bytes                     |
| `model_validate_strings()` | Validates string-based dictionaries              |
| `model_dump()`             | Converts a model to a dictionary                 |
| `model_dump_json()`        | Converts a model to a JSON string                |
| `model_copy()`             | Copies a model                                   |
| `model_json_schema()`      | Generates JSON Schema                            |
| `model_construct()`        | Creates a model without validation               |
| `field_validator()`        | Validates one or more individual fields          |
| `model_validator()`        | Validates the complete model                     |
| `ValidationError`          | Represents validation failures                   |
| `ConfigDict`               | Controls model behavior                          |

---

# Pydantic V1 vs V2 Names

Many older examples use Pydantic V1 methods.

| Older Pydantic V1 | Current Pydantic V2      |
| ----------------- | ------------------------ |
| `.dict()`         | `.model_dump()`          |
| `.json()`         | `.model_dump_json()`     |
| `.parse_obj()`    | `.model_validate()`      |
| `.parse_raw()`    | `.model_validate_json()` |
| `.copy()`         | `.model_copy()`          |
| `.schema()`       | `.model_json_schema()`   |
| `@validator`      | `@field_validator`       |
| `@root_validator` | `@model_validator`       |

The current documentation identifies the `model_*` method names as the primary Pydantic V2 API. ([Pydantic Docs][1])

---

# Interview Answer

> Pydantic is a data validation and serialization library that uses Python type annotations to define schemas. I use `BaseModel` to define request, response, configuration, or domain-transfer objects; `Field` to define constraints; `field_validator` and `model_validator` for custom business rules; and `model_dump` or `model_dump_json` to serialize validated models. Invalid input produces a structured `ValidationError`. In FastAPI, Pydantic automatically validates incoming JSON, converts compatible values into the declared types, generates OpenAPI schemas, and serializes response objects.

## Key interview point

Pydantic does more than simply check input:

> It parses and validates incoming data so the **resulting model** conforms to the declared types and constraints. It may convert compatible input values unless strict validation is enabled. ([Pydantic Docs][1])

[1]: https://docs.pydantic.dev/latest/concepts/models/ "Models | Pydantic Docs"
[2]: https://docs.pydantic.dev/latest/concepts/fields/ "Fields | Pydantic Docs"
[3]: https://docs.pydantic.dev/latest/concepts/serialization/ "Serialization | Pydantic Docs"
[4]: https://docs.pydantic.dev/latest/concepts/validators/ "Validators | Pydantic Docs"
[5]: https://docs.pydantic.dev/latest/errors/errors/ "Error Handling | Pydantic Docs"
