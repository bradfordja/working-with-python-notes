data structure

This is one of the most common senior Python interview questions because it tests whether you understand how to choose the right data structure, not just how to use it.
A good interviewer isn't looking for the definition of a list or a set; they're looking to see whether you can explain why you chose one over another.

Python Collections Overview
```sh
Data Type	Ordered	Mutable	Duplicates	Fast Lookup	Typical Use Case
List	✅ Yes	✅ Yes	✅ Yes	❌ O(n)	Ordered collection of items
Tuple	✅ Yes	❌ No	✅ Yes	❌ O(n)	Fixed/read-only data
Set	❌ No	✅ Yes	❌ No	✅ O(1)	Unique values and membership tests
Dictionary	✅ Yes*	✅ Yes	Keys: ❌ Values: ✅	✅ O(1)	Key-value lookups
```
Note: Dictionaries preserve insertion order in Python 3.7+.

## 1. List
Think of a List as...
A shopping list
Milk
Eggs
Bread
Milk
Butter
Order matters.
Duplicates are allowed.

Example
```py
fruits = ["Apple", "Banana", "Orange"]

print(fruits)
```
Output
```py
['Apple', 'Banana', 'Orange']
```
Lists Can Change
```py
fruits.append("Grape")
['Apple', 'Banana', 'Orange', 'Grape']
```
Remove
```py
fruits.remove("Banana")
```
Access by Index
```py
print(fruits[0])
```
Output
Apple

When Should You Use a List?
Use a list when:
* Order matters
* You need indexing
* You add/remove items
* Duplicates are acceptable
Examples:
* Shopping cart
* Todo list
* Search results
* API response lists
* Employees

Java Equivalent
```java
ArrayList<String>
```

## 2. Tuple
Think of a Tuple as...
A passport
Once created...
You don't change it.

Example
```py
location = ("Houston", "Texas")
```
or
```py
employee = (
    1001,
    "John Smith",
    "Engineering"
)
```

Trying to modify it
```py
location[0] = "Dallas"
```
Produces
```py
TypeError
```
Why Use Tuples?
Because they're immutable.
Benefits:
* Safer
* Slightly faster
* Can be dictionary keys
* Good for fixed data

Common Uses
Coordinates
```py
point = (25.5, 80.2)
```
Database records
```py
employee = (
    id,
    first_name,
    salary
)
```
Returning multiple values
```py
def get_user():
    return ("John", 35)
```
Java equivalent
There isn't a built-in Java equivalent.
Closest concepts:
* Java Record
* Immutable object

## 3. Set
Think of a Set as...
A guest list.
Every person can appear only once.

Example
```py
emails = {
    "john@gmail.com",
    "mary@gmail.com",
    "john@gmail.com"
}
```
Output
```py
{
    "john@gmail.com",
    "mary@gmail.com"
}
```
Duplicate removed automatically.

Fast Membership Testing
```py
if "john@gmail.com" in emails:
    print("Exists")
```
This is extremely fast.

Remove Duplicates
Suppose
```py
names = [
    "Bob",
    "Bob",
    "Alice",
    "John",
    "John"
]
```
Easy solution
```py
unique = set(names)
```
Result
```py
{
    "Bob",
    "Alice",
    "John"
}
```
Common Uses
* Remove duplicates
* Fast lookup
* Unique usernames
* Tags
* Permissions
* Visited URLs

Java Equivalent
HashSet

## 4. Dictionary
Think of a Dictionary as...
A phone book.
Instead of searching every page...
You look up the person's name.

Example
```py
employee = {
    "id": 100,
    "name": "John",
    "salary": 85000
}
```
Retrieve values
```py
print(employee["name"])
```
Output
John

Add new value
```py
employee["department"] = "IT"
```

Result
```py
{
    "id":100,
    "name":"John",
    "salary":85000,
    "department":"IT"
}
```
Why Use a Dictionary?
Fast lookup.
Instead of searching a list...
for employee in employees:
You can do
employees[100]
Almost instantly.

Common Uses
* JSON
* API requests
* API responses
* Configuration
* Caching
* User lookup
* Product lookup

Java Equivalent
HashMap

Real World Example
Imagine an employee system.
List
```py
employees = [
    "John",
    "Mary",
    "Bob"
]
```
You only care about order.

Tuple
```py
employee = (
    100,
    "John",
    85000
)
```
Fixed employee record.

Set
```py
skills = {
    "Python",
    "Java",
    "Python",
    "React"
}
```
Output
```json
{
    "Python",
    "Java",
    "React"
}
```
No duplicates.

Dictionary
```py
employee = {
    "id":100,
    "name":"John",
    "salary":85000
}
```
Lookup
```py
employee["salary"]
```
Output
85000

Big-O Performance
```sh
Operation	List	Tuple	Set	Dictionary
Index Access	O(1)	O(1)	❌	O(1) by key
Search (in)	O(n)	O(n)	O(1) average	O(1) average
Insert	O(1) end / O(n) middle	❌	O(1) average	O(1) average
Delete	O(n)	❌	O(1) average	O(1) average
```
How Senior Developers Choose
Suppose you're building a shopping application.
Products
```py
products = [
    "Laptop",
    "Mouse",
    "Keyboard"
]
```
List because order matters.

User IDs
```py
logged_in_users = {
    101,
    202,
    303
}
```
Set because you're constantly checking:
if user_id in logged_in_users:

Product Lookup
products = {
    1001: "Laptop",
    1002: "Keyboard",
    1003: "Mouse"
}
Dictionary because lookup by product ID is fast.

GPS Coordinates
coordinate = (
    29.7604,
    -95.3698
)
Tuple because coordinates shouldn't change.

Interview Question
Interviewer
When would you choose a Set over a List?
Junior Answer
"Because a Set doesn't allow duplicates."
That's correct, but incomplete.

Senior Answer
"I use a Set when uniqueness is required or when I need fast membership testing. Since Sets are hash-based, checking whether an element exists is typically O(1), whereas searching a List is O(n). For example, if I'm validating whether a username has already been seen, or tracking visited nodes in a graph traversal, a Set is the appropriate choice. If order or duplicates matter, I would choose a List instead."

Memory Trick
Think of these four like real-world objects:
* 📋 List → Shopping list (ordered, can repeat items)
* 📄 Tuple → Passport or contract (fixed, shouldn't change)
* 🎟️ Set → Guest list (unique entries only)
* 📖 Dictionary → Phone book or contact list (look up information by a key)
This analogy helps you quickly choose the right data structure during interviews and when designing real applications.
