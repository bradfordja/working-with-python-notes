# Sets
**Sets** are mutable collections of unique (hashable) items, commonly used for membership testing, removing duplicates, and performing mathematical operations like unions, intersections, and set differences.

**Common Methods:**
## **add(element)**: Adds an element to a set.
   ```python
   colors = {'red', 'green', 'blue'}
   colors.add('yellow')
   print(colors)  # Output may vary in order, e.g., {'red', 'blue', 'green', 'yellow'}
   ```

## **remove(element)**: Removes an element from the set; raises a KeyError if not present.
   ```python
   colors = {'red', 'green', 'blue'}
   colors.remove('red')
   print(colors)  # Output may vary in order, e.g., {'blue', 'green'}
   ```

## **discard(element)**: Removes an element from the set if it is a member (no error if not present).
   ```python
   colors = {'red', 'green', 'blue'}
   colors.discard('purple')  # Does not raise an error
   print(colors)  # Output may vary in order, e.g., {'red', 'blue', 'green'}
   ```

## **union(other)**: Returns a new set with elements from the set and `other`.
   ```python
   a = {1, 2, 3}
   b = {3, 4, 5}
   c = a.union(b)
   print(c)  # Output: {1, 2, 3, 4, 5}
   ```

## **intersection(other)**: Returns a new set with elements common to the set and `other`.
   ```python
   a = {1, 2, 3}
   b = {3, 4, 5}
   c = a.intersection(b)
   print(c)  # Output: {3}
   ```

