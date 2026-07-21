#Inheritance

Inheritance allows one class to inherit the attributes and methods of another class. This promotes code reusability.

**Example:**
```python
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        raise NotImplementedError("Subclass must implement abstract method")

class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

dog = Dog("Rex")
cat = Cat("Whiskers")
print(dog.speak())  # Output: Rex says Woof!
print(cat.speak())  # Output: Whiskers says Meow!
```
