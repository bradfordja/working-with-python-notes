Class and Instance Variables

- **Class variables** are shared among all instances of a class. They're defined within a class but outside any methods.
- **Instance variables** are variables for which each instantiated object of the class has its own separate copy, or instance.

**Example:**
```python
class Dog:
    species = "Canis familiaris"  # Class variable

    def __init__(self, name, age):
        self.name = name  # Instance variable
        self.age = age    # Instance variable
```

**Example:**
```python
class Cat:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return f"{self.name} says Woof!"

# Creating an instance of Dog
my_cat = Cat("Snowball")
print(my_cat.speak())  # Output: Snowball says Woof!
```
