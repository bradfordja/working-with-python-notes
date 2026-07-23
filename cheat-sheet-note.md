# cheat sheet
This is an excellent interview checklist. Below is a senior-level interview cheat sheet with concise answers you can use during technical interviews. I've also included follow-up points that interviewers commonly ask.

Python Senior Interview Cheat Sheet

## 1. Core Python Concepts

if Statements
Definition

Execute code conditionally based on a Boolean expression.
```py
age = 20

if age >= 18:
    print("Adult")
elif age >= 13:
    print("Teen")
else:
    print("Child")
```
Interview Answer
Python uses indentation instead of braces to define code blocks. if, elif, and else allow branching based on Boolean conditions.
```py
for Loops
for name in ["Alice", "Bob", "John"]:
    print(name)
```
Commonly used with:
* lists
* tuples
* sets
* dictionaries
* generators

Interview Answer
Python's for loop iterates over any iterable object rather than using index-based loops like Java.
```py
while Loops
count = 1

while count <= 5:
    print(count)
    count += 1
```
Use when:
* condition-driven loops
* polling
* retry logic

Functions
```py
def calculate_total(price, tax):
    return price + tax
```
Features:
* first-class objects
* optional parameters
* keyword arguments
* type hints
* variable arguments (*args, **kwargs)

## 2. Data Types

List
Use when:
* order matters
* duplicates allowed
* frequently modified
```py
employees = ["John", "Mary"]
```
Java equivalent:
ArrayList

Tuple
Use when:
* data should not change
* coordinates
* returning multiple values
```py
point = (10, 20)
```
Immutable.

Set
Use when:
* uniqueness matters
* fast lookup
* removing duplicates
```py
visited = {"A", "B", "C"}
```
Hash-based lookup.

Dictionary
Use when:
* key/value lookup
employee = {
    "id": 100,
    "name": "John"
}
HashMap equivalent.

NamedTuple
from collections import namedtuple
```py
Employee = namedtuple(
    "Employee",
    ["id", "name"]
)

employee = Employee(1, "John")
```
Use when:
* immutable object
* lightweight
* readable field names

defaultdict
```py
from collections import defaultdict

counts = defaultdict(int)

counts["apple"] += 1
```
Automatically creates missing values.
Common interview use:
* frequency counting
* grouping

## 3. Object-Oriented Programming

Creating Classes
```py
class Employee:

    def __init__(self, name):
        self.name = name
```
Concepts:
* encapsulation
* inheritance
* polymorphism
* abstraction

Dunder Methods
Examples:
```py
__init__()

__str__()

__repr__()

__len__()

__eq__()

__iter__()
```
Interview answer:
Dunder methods define how custom objects behave with Python operators and built-in functions.

## 4. File Handling

Reading Files
```py
with open("employees.txt") as file:
    data = file.read()
```
Always prefer:
with
because it automatically closes the file.

JSON

Read
```py
import json

with open("user.json") as file:
    data = json.load(file)
```
Write
```py
with open("user.json", "w") as file:
    json.dump(data, file)
```
## 5. Error Handling

Try/Except
```py
try:
    result = 10 / 0

except ZeroDivisionError:
    print("Cannot divide by zero")
```
Multiple Exceptions
```py
try:
    ...

except ValueError:
    ...

except FileNotFoundError:
    ...

Finally
try:
    ...

finally:
    print("Cleanup")
```
Always executes.
Use for:
* closing database connections
* releasing files
* cleanup

## 6. Memory Management
Python uses:
* reference counting
* garbage collection
Interview answer:
CPython primarily uses reference counting and supplements it with a cyclic garbage collector to reclaim objects involved in reference cycles.

Related interview topics:
* GIL
* shallow copy
* deep copy

## 7. Language Paradigm
Python is:
* Object-Oriented
* Functional
* Procedural
* Dynamically Typed
* Interpreted
* High-Level

Interview answer:
Python is a multi-paradigm language supporting object-oriented, functional, and procedural programming.

## 8. Concurrency & Parallelism

Threading
Best for:
* I/O-bound tasks

Examples:
* APIs
* databases
* file downloads

Affected by:
* GIL

Multiprocessing

Best for:
* CPU-bound work
Each process has its own:
* memory
* interpreter
* GIL

Async Programming
```py
async def get_users():
    ...
```
Uses:
* event loop
* await

Excellent for:
* FastAPI
* HTTP requests
* databases

# 9. Python-Specific Features

Decorators
Modify behavior without changing the function.
@login_required
```py
def dashboard():
    ...
```
Common examples:
* logging
* authentication
* caching
* timing

Generators
```py
def numbers():
    yield 1
    yield 2
```
Advantages:
* lazy evaluation
* low memory usage

Context Managers
```py
with open("data.txt") as file:
    ...
Uses
__enter__()

__exit__()
```
Automatically manages resources.

List Comprehensions
```py
squares = [
    x * x
    for x in range(10)
]
```
Readable way to create collections.

What Makes Python Unique?
* indentation
* dynamic typing
* batteries included
* readable syntax
* huge ecosystem
* duck typing
* first-class functions
* dunder methods
* decorators
* generators

## 10. Libraries & Frameworks

Django
Common interview topics:
* MTV architecture
* ORM
* Models
* Views
* Templates
* URL routing
* Middleware
* Authentication
* Admin
* Migrations

Flask
Topics:
* Blueprints
* Extensions
* REST APIs
* SQLAlchemy
* Jinja2

FastAPI
Topics:
* async endpoints
* dependency injection
* Pydantic validation
* OpenAPI
* JWT
* OAuth2
* Background tasks
* Middleware

Common Libraries
```sh
Library	Purpose
requests	HTTP client
pandas	Data analysis
numpy	Numerical computing
sqlalchemy	ORM
pytest	Testing
asyncio	Async programming
json	JSON parsing
pathlib	File system paths
collections	Specialized containers
typing	Type hints
logging	Application logging
```
Most Common Senior Python Interview Questions
```table
1. Explain the GIL.
2. Threading vs Multiprocessing.
3. Async vs Threading.
4. What are decorators?
5. What are generators?
6. is vs ==.
7. Mutable vs Immutable.
8. List vs Tuple.
9. Set vs Dictionary.
10. What are dunder methods?
11. What is duck typing?
12. Explain context managers.
13. Explain list comprehensions.
14. How does Python manage memory?
15. Explain shallow copy vs deep copy.
16. What is the difference between @staticmethod and @classmethod?
17. Explain *args and **kwargs.
18. How does exception handling work?
19. How do you structure a production FastAPI or Django application?
20. How do you optimize Python code for performance?
```

Senior Interview Closing Statement
If you're interviewing for a senior Python backend role (especially using FastAPI or Django), interviewers typically expect you to demonstrate proficiency in:
* Writing clean, Pythonic code that follows PEP 8 conventions.
* Choosing the appropriate data structures (lists, tuples, sets, dictionaries) based on performance and maintainability.
* Applying object-oriented principles and understanding Python's object model, including dunder methods.
* Using decorators, generators, context managers, and comprehensions effectively.
* Understanding concurrency options (threading, multiprocessing, and asyncio) and when each is appropriate.
* Handling exceptions consistently, managing resources safely, and understanding Python's memory model.
* Building maintainable web applications using frameworks like FastAPI or Django with proper validation, testing, logging, and modular architecture.
Being able to explain why you chose a particular approach—not just how it works—is what distinguishes a senior-level candidate.


# Senior Python Interview Answers

## 1. Explain the GIL

The Global Interpreter Lock is a CPython mechanism that allows only one thread at a time to execute Python bytecode within a process. It simplifies memory management but limits CPU-bound multithreading. Threads still work well for I/O-bound operations because the GIL is released while waiting on network, file, or database operations.

## 2. Threading vs Multiprocessing

Threading runs multiple threads inside one process and shares memory, making it suitable for I/O-bound work. Multiprocessing runs separate processes with independent memory and interpreters, allowing true CPU parallelism and making it better for CPU-intensive work.

## 3. Async vs Threading

Async programming uses a single event loop and cooperative task switching, making it efficient for handling many concurrent I/O operations. Threading uses operating-system threads and is useful for blocking libraries that do not support async. Async usually has lower overhead, but all operations must avoid blocking the event loop.

## 4. What are decorators?

Decorators are functions that wrap another function or class to extend its behavior without modifying its original implementation. They are commonly used for authentication, logging, caching, authorization, retries, and performance measurement.

```python
@require_authentication
def dashboard():
    return "Dashboard"
```

## 5. What are generators?

Generators produce values lazily using `yield` instead of creating an entire collection in memory. They are useful for large datasets, file streaming, pipelines, and memory-efficient iteration.

```python
def numbers():
    for value in range(3):
        yield value
```

## 6. `is` vs `==`

`==` compares values, while `is` compares object identity—whether two variables reference the exact same object. Use `is` primarily for singleton comparisons such as `value is None`.

## 7. Mutable vs Immutable

Mutable objects can be changed after creation, while immutable objects cannot. Lists, sets, and dictionaries are mutable; strings, integers, floats, and tuples are immutable. Mutability affects copying, function arguments, hashing, and thread safety.

## 8. List vs Tuple

A list is mutable and should be used when items need to be added, removed, or updated. A tuple is immutable and is appropriate for fixed records, coordinates, or values that should not change. Tuples can also be dictionary keys when all their elements are hashable.

## 9. Set vs Dictionary

A set stores unique values and is useful for duplicate removal and fast membership testing. A dictionary stores key-value pairs and is used when values must be retrieved by a unique key. Both are hash-based and provide average O(1) lookup.

## 10. What are dunder methods?

Dunder methods are special methods with double underscores that define how custom objects interact with Python syntax and built-in functions. Examples include `__init__`, `__str__`, `__eq__`, `__len__`, and `__iter__`.

## 11. What is duck typing?

Duck typing means Python focuses on an object's behavior rather than its declared type. If an object provides the required methods or attributes, it can be used regardless of its class hierarchy.

> If it behaves like the expected object, Python can use it.

## 12. Explain context managers

Context managers manage resource setup and cleanup around a block of code. They are commonly used with files, database connections, locks, and transactions. They implement `__enter__` and `__exit__` and are used with the `with` statement.

```python
with open("data.txt") as file:
    content = file.read()
```

## 13. Explain list comprehensions

List comprehensions provide a concise way to create lists by combining iteration, transformation, and optional filtering.

```python
squares = [number * number for number in range(10) if number % 2 == 0]
```

They are Pythonic for simple transformations, but normal loops are preferable when the logic becomes complex.

## 14. How does Python manage memory?

CPython primarily uses reference counting to track objects and automatically releases them when their reference count reaches zero. It also has a cyclic garbage collector for objects that reference each other. Python manages objects in a private heap through its memory allocator.

## 15. Explain shallow copy vs deep copy

A shallow copy creates a new outer object but keeps references to nested objects. A deep copy recursively copies the outer object and its nested objects.

```python
import copy

shallow = copy.copy(original)
deep = copy.deepcopy(original)
```

Changing a nested value may affect both objects after a shallow copy but not after a deep copy.

## 16. `@staticmethod` vs `@classmethod`

A static method receives neither the instance nor the class automatically and is used for utility behavior related to the class.

A class method receives the class as `cls` and is commonly used for alternative constructors or behavior that depends on class-level state.

```python
class User:
    @staticmethod
    def validate_email(email):
        return "@" in email

    @classmethod
    def from_json(cls, data):
        return cls(data["name"])
```

## 17. Explain `*args` and `**kwargs`

`*args` collects additional positional arguments into a tuple. `**kwargs` collects additional keyword arguments into a dictionary. They are useful for flexible APIs, wrappers, decorators, and forwarding arguments.

```python
def process(*args, **kwargs):
    print(args)
    print(kwargs)
```

## 18. How does exception handling work?

Python uses `try`, `except`, `else`, and `finally`.

* `try` contains code that may fail.
* `except` handles specific exceptions.
* `else` runs when no exception occurs.
* `finally` always runs and is used for cleanup.

```python
try:
    result = 10 / value
except ZeroDivisionError:
    result = 0
else:
    print("Calculation succeeded")
finally:
    print("Operation completed")
```

Production code should catch specific exceptions, preserve useful context, log appropriately, and avoid silently swallowing errors.

## 19. How do you structure a production FastAPI or Django application?

I separate the application into clear layers:

```text
Application
├── API routes or views
├── Request and response schemas
├── Services and business logic
├── Repositories or data-access layer
├── Database models
├── Authentication and authorization
├── Configuration
├── Exception handling
├── Logging and monitoring
└── Unit and integration tests
```

Routes or views should remain thin. Business logic belongs in services, and database access should be isolated behind repositories or ORM abstractions. Configuration and secrets should be environment-based, with centralized validation, logging, security, and testing.

## 20. How do you optimize Python code for performance?

I measure first using profiling tools rather than guessing. Common optimizations include:

* Choosing efficient algorithms and data structures
* Using sets and dictionaries for fast lookup
* Replacing unnecessary Python loops with vectorized NumPy or Pandas operations
* Using generators for large datasets
* Caching repeated calculations
* Reducing database and network calls
* Using async or threading for I/O-bound tasks
* Using multiprocessing for CPU-bound tasks
* Moving expensive work to optimized native libraries
* Monitoring memory usage and avoiding unnecessary object creation

The most important principle is to optimize the actual bottleneck while preserving code readability and correctness.
