# NumPy Library and Core Methods

**NumPy** is a Python library used for fast numerical computing. Its main data structure is the **`ndarray`**, which stores values in one-dimensional or multidimensional arrays.

```python
import numpy as np
```

## 1. `np.array()`

Creates a NumPy array.

```python
numbers = np.array([1, 2, 3, 4])
```

Use it when converting Python lists into efficient numerical arrays.

---

## 2. `np.arange()`

Creates a sequence of numbers using a start, stop, and step.

```python
numbers = np.arange(1, 10, 2)
```

Result:

```python
[1, 3, 5, 7, 9]
```

Similar to Python’s `range()`.

---

## 3. `np.linspace()`

Creates evenly spaced values between two endpoints.

```python
numbers = np.linspace(0, 10, 5)
```

Result:

```python
[0.0, 2.5, 5.0, 7.5, 10.0]
```

Useful for charts, simulations, and mathematical calculations.

---

## 4. `np.zeros()`

Creates an array filled with zeros.

```python
matrix = np.zeros((2, 3))
```

Useful for initializing arrays.

---

## 5. `np.ones()`

Creates an array filled with ones.

```python
matrix = np.ones((2, 3))
```

Useful for initialization and mathematical operations.

---

## 6. `np.full()`

Creates an array filled with a specific value.

```python
matrix = np.full((2, 2), 10)
```

Result:

```python
[[10, 10],
 [10, 10]]
```

---

## 7. `np.eye()`

Creates an identity matrix.

```python
matrix = np.eye(3)
```

Result:

```python
[[1., 0., 0.],
 [0., 1., 0.],
 [0., 0., 1.]]
```

Common in linear algebra.

---

## 8. `reshape()`

Changes the shape of an array without changing its values.

```python
numbers = np.array([1, 2, 3, 4, 5, 6])

matrix = numbers.reshape(2, 3)
```

Result:

```python
[[1, 2, 3],
 [4, 5, 6]]
```

---

## 9. `flatten()`

Converts a multidimensional array into a new one-dimensional array.

```python
flat = matrix.flatten()
```

---

## 10. `ravel()`

Also flattens an array, but often returns a view instead of a new copy.

```python
flat = matrix.ravel()
```

Use `ravel()` when memory efficiency matters.

---

## 11. `shape`

Returns the dimensions of the array.

```python
print(matrix.shape)
```

Example result:

```python
(2, 3)
```

This means two rows and three columns.

---

## 12. `size`

Returns the total number of elements.

```python
print(matrix.size)
```

---

## 13. `ndim`

Returns the number of dimensions.

```python
print(matrix.ndim)
```

Examples:

* One-dimensional array → `1`
* Matrix → `2`
* Three-dimensional array → `3`

---

## 14. `dtype`

Returns the data type of the array elements.

```python
print(numbers.dtype)
```

Examples:

```python
int64
float64
bool
```

---

## 15. `astype()`

Converts an array to another data type.

```python
numbers = numbers.astype(float)
```

Useful when converting integers to floats or strings to numbers.

---

## 16. `np.sum()`

Calculates the total.

```python
total = np.sum(numbers)
```

For a matrix:

```python
row_totals = np.sum(matrix, axis=1)
column_totals = np.sum(matrix, axis=0)
```

---

## 17. `np.mean()`

Calculates the average.

```python
average = np.mean(numbers)
```

---

## 18. `np.median()`

Returns the middle value.

```python
middle = np.median(numbers)
```

---

## 19. `np.min()`

Returns the smallest value.

```python
minimum = np.min(numbers)
```

---

## 20. `np.max()`

Returns the largest value.

```python
maximum = np.max(numbers)
```

---

## 21. `np.std()`

Calculates standard deviation.

```python
standard_deviation = np.std(numbers)
```

It measures how spread out the values are.

---

## 22. `np.var()`

Calculates variance.

```python
variance = np.var(numbers)
```

Variance is another measurement of data spread.

---

## 23. `np.argmin()`

Returns the index of the smallest value.

```python
index = np.argmin(numbers)
```

---

## 24. `np.argmax()`

Returns the index of the largest value.

```python
index = np.argmax(numbers)
```

---

## 25. `np.sort()`

Returns a sorted copy of an array.

```python
sorted_numbers = np.sort(numbers)
```

---

## 26. `np.unique()`

Returns unique values and removes duplicates.

```python
values = np.array([1, 2, 2, 3, 3, 4])

unique_values = np.unique(values)
```

Result:

```python
[1, 2, 3, 4]
```

---

## 27. `np.concatenate()`

Combines arrays along an existing axis.

```python
first = np.array([1, 2])
second = np.array([3, 4])

combined = np.concatenate((first, second))
```

---

## 28. `np.vstack()`

Stacks arrays vertically.

```python
result = np.vstack((first, second))
```

Result:

```python
[[1, 2],
 [3, 4]]
```

---

## 29. `np.hstack()`

Stacks arrays horizontally.

```python
result = np.hstack((first, second))
```

Result:

```python
[1, 2, 3, 4]
```

---

## 30. `np.split()`

Splits an array into smaller arrays.

```python
numbers = np.array([1, 2, 3, 4])

parts = np.split(numbers, 2)
```

---

## 31. `np.where()`

Returns values or indexes based on a condition.

```python
numbers = np.array([10, 20, 30, 40])

result = np.where(numbers > 20, numbers, 0)
```

Result:

```python
[0, 0, 30, 40]
```

---

## 32. Boolean Filtering

Filters values using conditions.

```python
numbers = np.array([10, 20, 30, 40])

filtered = numbers[numbers > 20]
```

Result:

```python
[30, 40]
```

---

## 33. `np.any()`

Returns `True` if at least one value matches a condition.

```python
result = np.any(numbers > 30)
```

---

## 34. `np.all()`

Returns `True` if all values match a condition.

```python
result = np.all(numbers > 0)
```

---

## 35. `np.dot()`

Performs dot-product or matrix multiplication.

```python
result = np.dot(first_matrix, second_matrix)
```

The `@` operator is also commonly used:

```python
result = first_matrix @ second_matrix
```

---

## 36. `.T`

Transposes an array by converting rows into columns.

```python
transposed = matrix.T
```

---

## 37. `np.transpose()`

Another way to transpose an array.

```python
transposed = np.transpose(matrix)
```

---

## 38. `np.random.default_rng()`

Creates a modern random-number generator.

```python
rng = np.random.default_rng(42)
```

The number `42` is the seed, which makes results repeatable.

---

## 39. `rng.integers()`

Creates random integers.

```python
numbers = rng.integers(1, 100, size=5)
```

---

## 40. `rng.random()`

Creates random floating-point values between `0` and `1`.

```python
values = rng.random(5)
```

---

## 41. `np.isnan()`

Checks for missing numerical values represented by `NaN`.

```python
data = np.array([10, np.nan, 30])

result = np.isnan(data)
```

---

## 42. `np.nanmean()`

Calculates the average while ignoring `NaN` values.

```python
average = np.nanmean(data)
```

Related methods include:

```python
np.nansum()
np.nanmin()
np.nanmax()
```

---

## 43. `np.clip()`

Limits values to a minimum and maximum range.

```python
numbers = np.array([5, 15, 25, 35])

result = np.clip(numbers, 10, 30)
```

Result:

```python
[10, 15, 25, 30]
```

---

## 44. `np.round()`

Rounds numerical values.

```python
values = np.array([1.234, 2.567])

rounded = np.round(values, 2)
```

Result:

```python
[1.23, 2.57]
```

---

## 45. `np.abs()`

Returns absolute values.

```python
numbers = np.array([-5, 10, -20])

result = np.abs(numbers)
```

Result:

```python
[5, 10, 20]
```

---

# Short Interview Answer

> NumPy is the core Python library for numerical computing. It provides the `ndarray` data structure and supports vectorized calculations, matrix operations, reshaping, filtering, aggregation, statistics, and random-number generation. I use NumPy when working with large numerical datasets because its operations are generally faster and more memory-efficient than standard Python lists and loops.
