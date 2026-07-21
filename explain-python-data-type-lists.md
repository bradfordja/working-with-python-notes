# Lists
**Lists** are mutable sequences, typically used to store collections of homogeneous items.

**Common Methods:**
## **append(x)**: Adds an item `x` to the end of the list.
   ```python
   numbers = [1, 2, 3]
   numbers.append(4)
   print(numbers)  # Output: [1, 2, 3, 4]
   ```

## **extend(iterable)**: Extends the list by appending all the items from the iterable.
   ```python
   numbers = [1, 2, 3]
   more_numbers = [4, 5]
   numbers.extend(more_numbers)
   print(numbers)  # Output: [1, 2, 3, 4, 5]
   ```

## **pop(index=-1)**: Removes and returns an item at a given position, defaulting to the last item if no index is specified.
   ```python
   fruits = ['apple', 'banana', 'cherry']
   last_fruit = fruits.pop()
   print(last_fruit)  # Output: 'cherry'
   print(fruits)      # Output: ['apple', 'banana']
   ```

## **remove(value)**: Removes the first occurrence of a value.
   ```python
   letters = ['a', 'b', 'c', 'd', 'b', 'e']
   letters.remove('b')
   print(letters)  # Output: ['a', 'c', 'd', 'b', 'e']
   ```

## **sort(key=None, reverse=False)**: Sorts the items of the list (the list is modified in place).
   ```python
   numbers = [3, 1, 4, 1, 5, 9, 2]
   numbers.sort()
   print(numbers)  # Output: [1, 1, 2, 3, 4, 5, 9]
   ```

