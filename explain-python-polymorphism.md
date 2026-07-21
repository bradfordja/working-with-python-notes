# Polymorphism

Polymorphism allows methods to do different things based on the object it is acting upon.

**Example:**
```python
class Animal:
    def speak(self):
        pass

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

animals = [Dog(), Cat()]
for animal in animals:
    print(animal.speak())  # Outputs "Woof!" then "Meow!"
```

