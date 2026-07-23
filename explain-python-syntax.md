# Python syntax
Since you already have a strong JavaScript and Java background, the biggest challenge isn't learning programming concepts—it's getting comfortable with Python's **unique syntax and idioms**. Python often has a much shorter and more expressive way of doing the same thing.

Below are the Python syntax features that JavaScript developers are most often asked about in interviews.

---

# 1. Indentation Instead of Braces

## Description

Unlike JavaScript, Python uses **indentation** (spaces) to define code blocks. There are no `{}` braces.

### JavaScript

```javascript
if (age >= 18) {
    console.log("Adult");
} else {
    console.log("Minor");
}
```

### Python

```python
if age >= 18:
    print("Adult")
else:
    print("Minor")
```

Notice:

* No parentheses required around the condition
* No braces
* Colon (`:`) starts a block
* Indentation defines the block

---

### Interview Question

**Q:** Why does Python use indentation instead of braces?

**Answer:**

> Python uses indentation to make code consistently readable. Since indentation is mandatory, all Python code follows a similar structure, reducing formatting inconsistencies.

---

# 2. No Semicolons

### JavaScript

```javascript
let x = 10;
let y = 20;
```

### Python

```python
x = 10
y = 20
```

Semicolons are optional but rarely used.

---

# 3. Dynamic Typing

### JavaScript

```javascript
let name = "John";
name = 100;
```

### Python

```python
name = "John"
name = 100
```

Python variables can reference different object types during execution.

---

### Interview Question

**Q:** Is Python statically typed?

**Answer:**

> No. Python is dynamically typed. Variable types are determined at runtime.

---

# 4. Everything Is an Object

```python
x = 10

print(type(x))
```

Output

```python
<class 'int'>
```

Even integers are objects.

---

### JavaScript Comparison

```javascript
typeof 10
```

returns

```
number
```

---

# 5. Multiple Assignment

### JavaScript

```javascript
let a = 1;
let b = 2;
```

### Python

```python
a, b = 1, 2
```

---

## Swapping Variables

JavaScript

```javascript
let temp = a;
a = b;
b = temp;
```

Modern JavaScript

```javascript
[a, b] = [b, a];
```

Python

```python
a, b = b, a
```

No temporary variable required.

---

### Interview Question

**Q:** How do you swap two variables in Python?

```python
a, b = b, a
```

---

# 6. Membership Operator (`in`)

### JavaScript

```javascript
if (numbers.includes(5)) {
}
```

### Python

```python
if 5 in numbers:
    print("Found")
```

Works with:

* list
* tuple
* set
* dictionary (checks keys)
* string

---

### Example

```python
name = "Julio"

if "J" in name:
    print("Yes")
```

---

# 7. `not in`

```python
if "Admin" not in roles:
    print("Access denied")
```

---

# 8. Identity vs Equality

One of the most common Python interview questions.

### JavaScript

```javascript
===

==
```

### Python

```python
==

is
```

---

## Equality

```python
a = [1,2]
b = [1,2]

print(a == b)
```

Output

```
True
```

Values are equal.

---

## Identity

```python
print(a is b)
```

Output

```
False
```

Different objects in memory.

---

### JavaScript Equivalent

```javascript
[] === []
```

returns

```
false
```

because objects are compared by reference.

---

### Interview Question

**Q:** Difference between `==` and `is`?

Answer

> `==` compares values. `is` compares object identity (whether both variables reference the same object in memory).

---

# 9. None vs null

JavaScript

```javascript
null
```

Python

```python
None
```

Example

```python
employee = None
```

---

Interview Question

**Q:** What is `None`?

Answer

> `None` represents the absence of a value. It is Python's singleton null object.

---

# 10. Truthy and Falsy

Python

```python
if []:
    print("True")
```

Nothing prints.

Why?

Empty lists are considered `False`.

Falsy values include:

```python
None

0

0.0

False

''

[]

{}

set()
```

Everything else is generally truthy.

---

### JavaScript Comparison

JavaScript also has truthy/falsy values, but `undefined`, `null`, and `NaN` have their own behavior.

---

# 11. List Comprehension

One of Python's signature features.

JavaScript

```javascript
const squares = numbers.map(x => x * x);
```

Python

```python
squares = [x*x for x in numbers]
```

---

Filtering

JavaScript

```javascript
numbers.filter(x => x > 10)
```

Python

```python
[x for x in numbers if x > 10]
```

---

### Interview Question

**Q:** Why use list comprehensions?

Answer

* Cleaner
* More readable
* Often faster than an explicit loop
* Idiomatic Python

---

# 12. Slicing

JavaScript

```javascript
array.slice(1,4)
```

Python

```python
numbers[1:4]
```

---

Examples

First 3

```python
numbers[:3]
```

Last 3

```python
numbers[-3:]
```

Every other item

```python
numbers[::2]
```

Reverse

```python
numbers[::-1]
```

---

Interview Question

**Q:** How do you reverse a list?

```python
numbers[::-1]
```

---

# 13. Dictionary vs JavaScript Object

JavaScript

```javascript
const employee = {
    name: "John"
}
```

Python

```python
employee = {
    "name": "John"
}
```

Access

```python
employee["name"]
```

Safer

```python
employee.get("name")
```

Returns `None` if the key doesn't exist instead of raising a `KeyError`.

---

# 14. For Loop

JavaScript

```javascript
for(let i=0;i<5;i++)
```

Python

```python
for i in range(5):
    print(i)
```

Output

```
0
1
2
3
4
```

---

# 15. Enumerate

Instead of manually managing an index:

```python
names = ["Alice", "Bob", "Charlie"]

for index, name in enumerate(names):
    print(index, name)
```

Output

```
0 Alice
1 Bob
2 Charlie
```

---

# 16. Zip

Combine multiple sequences into pairs.

```python
names = ["Alice", "Bob"]
ages = [30, 25]

for name, age in zip(names, ages):
    print(name, age)
```

Output

```
Alice 30
Bob 25
```

This is commonly used when processing related collections.

---

# 17. Unpacking

Python can unpack tuples and lists directly.

```python
person = ("Julio", 56)

name, age = person

print(name)
print(age)
```

---

You can also ignore values:

```python
first, _, third = (1, 2, 3)

print(first)
print(third)
```

The `_` is a common convention for "I don't need this value."

---

# Senior Interview Tips for JavaScript Developers

If you're interviewing as a senior engineer, interviewers usually don't expect you to recite syntax—they expect you to know when and why to use Python features.

For example:

| Feature              | Good Senior Talking Point                                                                |
| -------------------- | ---------------------------------------------------------------------------------------- |
| List comprehension   | "I use it for concise transformations when readability remains high."                    |
| Generators (`yield`) | "I use generators to process large datasets without loading everything into memory."     |
| `==` vs `is`         | "I use `is` when checking singletons like `None`; otherwise I compare values with `==`." |
| Dictionary `.get()`  | "It avoids `KeyError` and simplifies handling optional keys."                            |
| `enumerate()`        | "It's cleaner and less error-prone than manually tracking an index."                     |
| `zip()`              | "It's useful for iterating over related collections in parallel."                        |
| Slicing              | "It's an efficient and expressive way to extract or reverse portions of sequences."      |

## Common Interview Question

**Q:** *As an experienced JavaScript developer, what Python features did you find most valuable?*

**Strong Answer:**

> "The features that stand out are list comprehensions, dictionary operations, slicing, generators, and Python's readable syntax. Compared to JavaScript, many common data transformations require less code while remaining easy to understand. For data processing, automation, and ETL work, Python lets me express complex logic very concisely without sacrificing maintainability."

These are the Python language features that come up most frequently in senior Python interviews, especially for backend, data engineering, and Databricks-oriented roles.
