# Common Python Decorators
Here is the same content in copy-ready Markdown format.

## Common Python Decorators — Interview Prep

A decorator is a function or class that modifies the behavior of another function, method, or class without changing its original source code.

Basic Syntax
```py
@decorator_name
def my_function():
    pass
```
This is equivalent to:
```py
def my_function():
    pass
my_function = decorator_name(my_function)
```
⸻

## 1. @staticmethod

Description

Defines a method that belongs to a class but does not receive self or cls.

Use Case

Use it when a method is logically related to the class but does not need instance or class data.

Sample Code
```py
class Calculator:
    @staticmethod
    def add(a: int, b: int) -> int:
        return a + b
print(Calculator.add(5, 3))
```
Output

8

Interview Point

A static method behaves like a regular function placed inside a class for organization.

⸻

## 2. @classmethod

Description

Defines a method that receives the class itself as the first argument, conventionally named cls.

Use Case

Commonly used for:

* Alternative constructors
* Modifying class-level data
* Creating objects from different formats

Sample Code
```py
class Employee:
    company = "Tech Corp"
    def __init__(self, name: str, salary: float):
        self.name = name
        self.salary = salary
    @classmethod
    def from_string(cls, employee_data: str):
        name, salary = employee_data.split(",")
        return cls(name, float(salary))
employee = Employee.from_string("Julio,120000")
print(employee.name)
print(employee.salary)
```
Output

Julio
120000.0

Interview Point

A class method receives cls, while a normal instance method receives self.

⸻

## 3. @property

Description

Allows a method to be accessed like an attribute.

Use Case

Use it for:

* Computed values
* Encapsulation
* Validation
* Controlled access to internal data

Sample Code
```py
class Rectangle:
    def __init__(self, width: float, height: float):
        self.width = width
        self.height = height
    @property
    def area(self) -> float:
        return self.width * self.height
rectangle = Rectangle(10, 5)
print(rectangle.area)
```
Output

50

Without @property, you would call:

rectangle.area()

With @property, you access it like an attribute:

rectangle.area

⸻

## 4. @property with a Setter

Description

A property setter controls how an attribute is updated.

Use Case

Use it when values must be validated before assignment.

Sample Code
```py
class Product:
    def __init__(self, price: float):
        self.price = price
    @property
    def price(self) -> float:
        return self._price
    @price.setter
    def price(self, value: float) -> None:
        if value < 0:
            raise ValueError("Price cannot be negative")
        self._price = value
product = Product(100)
product.price = 150
print(product.price)
```
Output

150

Invalid assignment:
```py
product.price = -10

Raises:

ValueError: Price cannot be negative
```
⸻

## 5. @dataclass

Description

Automatically generates common class methods such as:

* __init__
* __repr__
* __eq__

It is available from Python’s dataclasses module.

Use Case

Use it for classes that primarily store data.

Sample Code
```py
from dataclasses import dataclass
@dataclass
class User:
    name: str
    email: str
    active: bool = True
user = User(
    name="Julio",
    email="julio@example.com"
)
print(user)
```
Output
```py
User(name='Julio', email='julio@example.com', active=True)
```
Without @dataclass, you would need to manually create the constructor and other common methods.

⸻

## 6. @dataclass(frozen=True)

Description

Creates an immutable data object.

Use Case

Useful for:

* Configuration objects
* Value objects
* Read-only domain models

Sample Code
```py
from dataclasses import dataclass
@dataclass(frozen=True)
class DatabaseConfig:
    host: str
    port: int
config = DatabaseConfig(
    host="localhost",
    port=5432
)
print(config.host)
```
Trying to modify the object:

config.port = 3306

Raises an error because the object is immutable.

⸻

## 7. @abstractmethod

Description

Marks a method that subclasses are required to implement.

It is used with abstract base classes.

Use Case

Use it to define interfaces or contracts that subclasses must follow.

Sample Code
```py
from abc import ABC, abstractmethod
class PaymentService(ABC):
    @abstractmethod
    def process_payment(self, amount: float) -> None:
        pass
class CreditCardPayment(PaymentService):
    def process_payment(self, amount: float) -> None:
        print(f"Processing credit card payment: ${amount}")
payment = CreditCardPayment()
payment.process_payment(100)
```
Output

Processing credit card payment: $100

You cannot instantiate PaymentService directly because it contains an abstract method.

⸻

## 8. @functools.wraps

Description

Preserves the original function’s metadata when creating a custom decorator.

It preserves information such as:

* Function name
* Documentation string
* Type annotations

Use Case

Use it inside almost every custom function decorator.

Sample Code
```py
from functools import wraps
def log_call(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling function: {func.__name__}")
        return func(*args, **kwargs)
    return wrapper
@log_call
def greet(name: str) -> str:
    """Returns a greeting message."""
    return f"Hello, {name}"
print(greet("Julio"))
print(greet.__name__)
print(greet.__doc__)
```
Output

Calling function: greet
Hello, Julio
greet
Returns a greeting message.

Without @wraps, the function name would usually appear as wrapper.

⸻

## 9. @lru_cache

Description

Caches function results so repeated calls with the same arguments do not recalculate the result.

LRU means Least Recently Used.

Use Case

Useful for:

* Expensive calculations
* Recursive algorithms
* Repeated lookups
* Configuration retrieval

Sample Code
```py
from functools import lru_cache
@lru_cache(maxsize=None)
def fibonacci(number: int) -> int:
    if number <= 1:
        return number
    return fibonacci(number - 1) + fibonacci(number - 2)
print(fibonacci(40))
```
Output

102334155

Without caching, the recursive function repeatedly calculates the same values.

You can inspect cache usage with:
```py
print(fibonacci.cache_info())
```
⸻

## 10. @cache

Description

Caches every function result without limiting the cache size.

It is similar to:
```py
@lru_cache(maxsize=None)
```
Use Case

Useful for pure functions whose results should be reused.

Sample Code
```py
from functools import cache
@cache
def calculate_tax(amount: float, rate: float) -> float:
    print("Calculating tax...")
    return amount * rate
print(calculate_tax(1000, 0.08))
print(calculate_tax(1000, 0.08))
```
Output

Calculating tax...
80.0
80.0

The calculation message appears only once because the second result comes from the cache.

⸻

## 11. @singledispatch

Description

Allows a function to behave differently depending on the type of its first argument.

Use Case

Use it when one logical operation requires different implementations for different data types.

Sample Code
```py
from functools import singledispatch
@singledispatch
def display(value):
    print(f"Default value: {value}")
@display.register
def _(value: int):
    print(f"Integer: {value}")
@display.register
def _(value: list):
    print(f"List with {len(value)} items: {value}")
display("Python")
display(100)
display([1, 2, 3])
```py
Output
```py
Default value: Python
Integer: 100
List with 3 items: [1, 2, 3]
```
⸻

## 12. @total_ordering

Description

Automatically generates comparison methods when you define __eq__ and one ordering method, such as __lt__.

Use Case

Useful for objects that need sorting or comparison.

Sample Code
```py
from functools import total_ordering
@total_ordering
class Employee:
    def __init__(self, name: str, salary: float):
        self.name = name
        self.salary = salary
    def __eq__(self, other):
        if not isinstance(other, Employee):
            return NotImplemented
        return self.salary == other.salary
    def __lt__(self, other):
        if not isinstance(other, Employee):
            return NotImplemented
        return self.salary < other.salary
employee1 = Employee("Alice", 90000)
employee2 = Employee("Bob", 120000)
print(employee1 < employee2)
print(employee2 > employee1)
```
Output

True
True

⸻

## 13. @contextmanager

Description

Converts a generator function into a context manager that can be used with the with statement.

Use Case

Useful for:

* Opening and closing resources
* Database transactions
* Temporary configuration
* Locks
* Connections

Sample Code
```py
from contextlib import contextmanager
@contextmanager
def managed_resource():
    print("Opening resource")
    try:
        yield "Resource is available"
    finally:
        print("Closing resource")
with managed_resource() as resource:
    print(resource)
```
Output

Opening resource
Resource is available
Closing resource

The code after yield runs even if an exception occurs inside the with block.

⸻

Common Custom Decorators

## 14. Logging Decorator

Description

Logs when a function starts and finishes.

Use Case

Useful for:

* Debugging
* Auditing
* Application monitoring
* Tracing function execution

Sample Code
```py
from functools import wraps
def log_execution(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Starting {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper
@log_execution
def add(a: int, b: int) -> int:
    return a + b
print(add(4, 6))
```
Output

Starting add
Finished add
10

⸻

## 15. Timing Decorator

Description

Measures how long a function takes to execute.

Use Case

Useful for:

* Performance testing
* Identifying slow operations
* Comparing algorithms
* Monitoring response time

Sample Code
```py
import time
from functools import wraps
def measure_time(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()
        result = func(*args, **kwargs)
        elapsed_time = time.perf_counter() - start_time
        print(
            f"{func.__name__} completed in "
            f"{elapsed_time:.6f} seconds"
        )
        return result
    return wrapper
@measure_time
def process_data():
    time.sleep(1)
    return "Completed"
print(process_data())
```
⸻

## 16. Authentication Decorator

Description

Checks whether a user is authenticated before allowing a function to run.

Use Case

Commonly used in:

* Web APIs
* Secure applications
* Protected routes
* User account operations

Sample Code
```py
from functools import wraps
def require_authentication(func):
    @wraps(func)
    def wrapper(user, *args, **kwargs):
        if not user.get("authenticated"):
            raise PermissionError(
                "Authentication is required"
            )
        return func(user, *args, **kwargs)
    return wrapper
@require_authentication
def view_profile(user):
    return f"Welcome, {user['name']}"
authenticated_user = {
    "name": "Julio",
    "authenticated": True
}
print(view_profile(authenticated_user))
```
Output

Welcome, Julio

⸻

## 17. Authorization Decorator with Arguments

Description

Checks whether a user has a required role.

This is a decorator factory because the decorator itself accepts an argument.

Use Case

Useful for:

* Role-based access control
* Admin-only operations
* Permission validation
* API security

Sample Code
```py
from functools import wraps
def require_role(required_role: str):
    def decorator(func):
        @wraps(func)
        def wrapper(user, *args, **kwargs):
            if user.get("role") != required_role:
                raise PermissionError(
                    f"{required_role} role required"
                )
            return func(user, *args, **kwargs)
        return wrapper
    return decorator
@require_role("admin")
def delete_user(user, user_id: int):
    return f"User {user_id} deleted by {user['name']}"
admin_user = {
    "name": "Julio",
    "role": "admin"
}
print(delete_user(admin_user, 101))
```
Output

User 101 deleted by Julio

⸻

## 18. Retry Decorator

Description

Retries a function when it fails.

Use Case

Useful for:

* Network requests
* Temporary database failures
* External API calls
* Unstable remote services

Sample Code
```py
import time
from functools import wraps
def retry(max_attempts: int = 3, delay: float = 1.0):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            last_error = None
            for attempt in range(1, max_attempts + 1):
                try:
                    return func(*args, **kwargs)
                except Exception as error:
                    last_error = error
                    print(
                        f"Attempt {attempt} failed: {error}"
                    )
                    if attempt < max_attempts:
                        time.sleep(delay)
            raise last_error
        return wrapper
    return decorator
@retry(max_attempts=3, delay=0.5)
def call_service():
    raise ConnectionError("Service unavailable")
call_service()
```
Interview Point

In production code, retry only exceptions that are temporary and safe to retry.

Avoid automatically retrying all exceptions.

⸻

## 19. Validation Decorator

Description

Validates function arguments before executing the original function.

Use Case

Useful for:

* Business rules
* Input validation
* API request validation
* Preventing invalid calculations

Sample Code
```
from functools import wraps
def require_positive_numbers(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        values = list(args) + list(kwargs.values())
        if any(
            isinstance(value, (int, float)) and value < 0
            for value in values
        ):
            raise ValueError(
                "Numbers must not be negative"
            )
        return func(*args, **kwargs)
    return wrapper
@require_positive_numbers
def calculate_total(price: float, quantity: int) -> float:
    return price * quantity
print(calculate_total(25.50, 4))
```
Output

102.0

⸻

## 20. Debugging Decorator

Description

Displays function arguments and return values.

Use Case

Useful during:

* Development
* Troubleshooting
* Interview demonstrations
* Unit-test debugging

Sample Code
```py
from functools import wraps
def debug(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Function: {func.__name__}")
        print(f"Arguments: {args}")
        print(f"Keyword arguments: {kwargs}")
        result = func(*args, **kwargs)
        print(f"Result: {result}")
        return result
    return wrapper
@debug
def multiply(a: int, b: int) -> int:
    return a * b
multiply(5, 4)
```
Output

Function: multiply
Arguments: (5, 4)
Keyword arguments: {}
Result: 20

⸻

How a Custom Decorator Works

The following decorator converts a function’s result to uppercase.
```py
from functools import wraps
def uppercase_result(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result.upper()
    return wrapper
@uppercase_result
def greeting(name: str) -> str:
    return f"Hello, {name}"
print(greeting("Julio"))
```
Output

HELLO, JULIO

Execution Flow

greeting("Julio")
        ↓
wrapper("Julio")
        ↓
original greeting function
        ↓
"Hello, Julio"
        ↓
.upper()
        ↓
"HELLO, JULIO"

⸻

## Applying Multiple Decorators

Decorators are applied from bottom to top.
```py
def decorator_one(func):
    def wrapper():
        return "One(" + func() + ")"
    return wrapper
def decorator_two(func):
    def wrapper():
        return "Two(" + func() + ")"
    return wrapper
@decorator_one
@decorator_two
def message():
    return "Hello"
print(message())
```
Output
```py
One(Two(Hello))
```
This is equivalent to:
```py
message = decorator_one(
    decorator_two(message)
)
```
The decorator closest to the function executes first during wrapping.

⸻

## Decorator with Arguments

A decorator with arguments requires three levels of functions:

1. Decorator factory
2. Decorator
3. Wrapper
```py
from functools import wraps
def repeat(times: int):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            result = None
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator
@repeat(times=3)
def greet(name: str) -> str:
    message = f"Hello, {name}"
    print(message)
    return message
greet("Julio")
```
Output

Hello, Julio
Hello, Julio
Hello, Julio

⸻

## Class-Based Decorator

A class can also be used as a decorator by implementing __call__.
```py
class CallCounter:
    def __init__(self, func):
        self.func = func
        self.count = 0
    def __call__(self, *args, **kwargs):
        self.count += 1
        print(
            f"{self.func.__name__} has been called "
            f"{self.count} time(s)"
        )
        return self.func(*args, **kwargs)
@CallCounter
def greet(name: str) -> str:
    return f"Hello, {name}"
print(greet("Julio"))
print(greet("Alice"))
```
Output

greet has been called 1 time(s)
Hello, Julio
greet has been called 2 time(s)
Hello, Alice

⸻

## Decorators on Instance Methods

A custom decorator can also wrap methods inside a class.
```py
from functools import wraps
def log_method(func):
    @wraps(func)
    def wrapper(self, *args, **kwargs):
        print(
            f"Calling {self.__class__.__name__}."
            f"{func.__name__}"
        )
        return func(self, *args, **kwargs)
    return wrapper
class UserService:
    @log_method
    def find_user(self, user_id: int) -> dict:
        return {
            "id": user_id,
            "name": "Julio"
        }
service = UserService()
print(service.find_user(101))
```
⸻

## Common Decorator Structure

A normal custom decorator generally follows this pattern:
```py
from functools import wraps
def my_decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        # Code before the original function
        result = func(*args, **kwargs)
        # Code after the original function
        return result
    return wrapper
```
A decorator that accepts arguments generally follows this pattern:
```py
from functools import wraps
def decorator_factory(option):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            print(f"Option: {option}")
            return func(*args, **kwargs)
        return wrapper
    return decorator
```
⸻

## Most Important Decorators for Interviews
```sh
Decorator	Main Purpose
@staticmethod	Defines a method without self or cls
@classmethod	Defines a method that receives the class
@property	Accesses a method like an attribute
@dataclass	Generates common class methods
@abstractmethod	Requires subclass implementation
@wraps	Preserves decorated function metadata
@lru_cache	Caches function results
@cache	Provides unlimited function caching
@singledispatch	Changes behavior based on argument type
@total_ordering	Generates comparison methods
@contextmanager	Creates a context manager
Custom logging decorator	Logs function execution
Custom timing decorator	Measures execution time
Custom authentication decorator	Protects functions from unauthenticated users
Custom authorization decorator	Enforces roles and permissions
Custom retry decorator	Retries temporary failures
Custom validation decorator	Validates function arguments
```
⸻

## Important Interview Questions

### What is a Python decorator?

A decorator is a callable that wraps another function, method, or class to add or modify behavior without changing the original implementation.

⸻

### What does @decorator syntax mean?

This:
```py
@my_decorator
def greet():
    return "Hello"
```
is equivalent to:
```py
def greet():
    return "Hello"
greet = my_decorator(greet)
```
⸻

## Why should custom decorators use functools.wraps?

functools.wraps preserves the original function’s:

* Name
* Documentation
* Type annotations
* Metadata

Example:
```py
from functools import wraps
def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper
```
⸻

## What are *args and **kwargs used for?

They allow a decorator to work with functions that accept different arguments.
```py
def wrapper(*args, **kwargs):
    return func(*args, **kwargs)
```
* *args collects positional arguments.
* **kwargs collects keyword arguments.

⸻

## What is a decorator factory?

A decorator factory is a function that creates and returns a decorator.

It is used when a decorator needs configuration arguments.
```py
def require_role(role):
    def decorator(func):
        def wrapper(*args, **kwargs):
            return func(*args, **kwargs)
        return wrapper
    return decorator
```
Usage:
```py
@require_role("admin")
def delete_user():
    pass
```
⸻

## Can decorators modify return values?

Yes.
```py
def double_result(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result * 2
    return wrapper
@double_result
def get_number():
    return 10
print(get_number())
```
Output:

20

⸻

## Can decorators prevent a function from running?

Yes. Authentication, authorization, and validation decorators commonly prevent execution.
```py
def require_admin(func):
    def wrapper(user, *args, **kwargs):
        if user.get("role") != "admin":
            raise PermissionError("Admin access required")
        return func(user, *args, **kwargs)
    return wrapper
```
⸻

## What order are multiple decorators applied?

Multiple decorators are applied from bottom to top.
```py
@decorator_one
@decorator_two
def function():
    pass
```
Equivalent code:
```py
function = decorator_one(
    decorator_two(function)
)
```
⸻

## Strong Interview Answer

A Python decorator is a callable that wraps another function, method, or class to add or modify behavior without changing the original implementation. Common built-in decorators include @staticmethod, @classmethod, @property, @dataclass, @abstractmethod, and @lru_cache. Custom decorators are frequently used for logging, authentication, authorization, validation, retries, caching, and performance measurement. When writing a function decorator, I normally use functools.wraps to preserve the original function’s metadata and use *args and **kwargs so the decorator can support different function signatures.

⸻

## Quick Reference

# Static method
@staticmethod
def method():
    pass
# Class method
@classmethod
def method(cls):
    pass
# Property getter
@property
def value(self):
    return self._value
# Property setter
@value.setter
def value(self, new_value):
    self._value = new_value
# Data class
@dataclass
class User:
    name: str
# Abstract method
@abstractmethod
def process(self):
    pass
# Cache
@lru_cache(maxsize=128)
def calculate(value):
    pass
# Custom decorator
def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper