# INIT

The `__init__` Method

`__init__` is a special method in Python classes. It's called automatically whenever a new instance of the class is created. It can take arguments to initialize the newly created object's attributes.

**Example:**
```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
```
