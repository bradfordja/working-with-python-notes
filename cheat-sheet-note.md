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

2. Data Types

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

3. Object-Oriented Programming

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

4. File Handling

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
5. Error Handling

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

10. Libraries & Frameworks

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
