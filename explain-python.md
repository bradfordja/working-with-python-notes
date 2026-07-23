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
