# TensorFlow Library and Core Methods

**TensorFlow** is an open-source machine learning and deep learning framework developed by Google. It is used to build, train, evaluate, and deploy AI models for tasks such as image recognition, natural language processing (NLP), recommendation systems, and time-series forecasting.

```python
import tensorflow as tf
```

---

# Core Concepts

TensorFlow revolves around four key concepts:

* **Tensor** – Multidimensional array (similar to a NumPy array)
* **Operations (Ops)** – Mathematical computations performed on tensors
* **Models** – Neural networks that learn patterns from data
* **Automatic Differentiation** – Calculates gradients for model training

---

# 1. `tf.constant()`

Creates an immutable tensor.

```python
import tensorflow as tf

x = tf.constant([1, 2, 3])
```

**Use Case**

* Fixed values
* Input data
* Labels
* Constants

---

# 2. `tf.Variable()`

Creates a mutable tensor whose values can change during training.

```python
weights = tf.Variable([0.5, 0.8])
```

**Use Case**

* Neural network weights
* Bias values
* Trainable parameters

---

# 3. `tf.convert_to_tensor()`

Converts Python objects or NumPy arrays into TensorFlow tensors.

```python
numbers = [1, 2, 3]

tensor = tf.convert_to_tensor(numbers)
```

**Use Case**

Preparing external data for TensorFlow operations.

---

# 4. `tf.zeros()`

Creates a tensor filled with zeros.

```python
matrix = tf.zeros((2, 3))
```

**Use Case**

Initialize weights or placeholder data.

---

# 5. `tf.ones()`

Creates a tensor filled with ones.

```python
matrix = tf.ones((2, 3))
```

---

# 6. `tf.fill()`

Creates a tensor filled with a specific value.

```python
matrix = tf.fill((2, 2), 10)
```

---

# 7. `tf.random.normal()`

Generates random values from a normal (Gaussian) distribution.

```python
weights = tf.random.normal((3, 3))
```

**Use Case**

Weight initialization.

---

# 8. `tf.random.uniform()`

Generates random values from a uniform distribution.

```python
numbers = tf.random.uniform((5,))
```

---

# 9. `tf.reshape()`

Changes the shape of a tensor.

```python
tensor = tf.constant([1,2,3,4,5,6])

matrix = tf.reshape(tensor, (2,3))
```

---

# 10. `tf.cast()`

Converts data types.

```python
x = tf.cast(x, tf.float32)
```

Common types:

* `tf.int32`
* `tf.float32`
* `tf.float64`
* `tf.bool`

---

# 11. `tf.reduce_sum()`

Calculates the sum.

```python
total = tf.reduce_sum(x)
```

---

# 12. `tf.reduce_mean()`

Calculates the average.

```python
average = tf.reduce_mean(x)
```

---

# 13. `tf.reduce_max()`

Returns the maximum value.

```python
maximum = tf.reduce_max(x)
```

---

# 14. `tf.reduce_min()`

Returns the minimum value.

```python
minimum = tf.reduce_min(x)
```

---

# 15. `tf.argmax()`

Returns the index of the largest value.

```python
index = tf.argmax(x)
```

Commonly used when selecting the predicted class.

---

# 16. `tf.argmin()`

Returns the index of the smallest value.

```python
index = tf.argmin(x)
```

---

# 17. `tf.matmul()`

Performs matrix multiplication.

```python
result = tf.matmul(A, B)
```

One of the most important operations in deep learning.

---

# 18. `tf.transpose()`

Swaps rows and columns.

```python
result = tf.transpose(matrix)
```

---

# 19. `tf.concat()`

Combines tensors.

```python
combined = tf.concat([A, B], axis=0)
```

---

# 20. `tf.stack()`

Stacks tensors into a new dimension.

```python
result = tf.stack([A, B])
```

---

# 21. `tf.expand_dims()`

Adds a dimension.

```python
image = tf.expand_dims(image, axis=0)
```

Useful for creating a batch dimension.

---

# 22. `tf.squeeze()`

Removes dimensions of size 1.

```python
image = tf.squeeze(image)
```

---

# 23. `tf.nn.relu()`

Applies the ReLU activation function.

```python
output = tf.nn.relu(x)
```

ReLU:

```
max(0, x)
```

---

# 24. `tf.nn.softmax()`

Converts outputs into probabilities.

```python
probabilities = tf.nn.softmax(logits)
```

Used in classification models.

---

# 25. `tf.nn.sigmoid()`

Applies the Sigmoid activation.

```python
result = tf.nn.sigmoid(x)
```

Produces values between 0 and 1.

Used for binary classification.

---

# 26. `tf.GradientTape()`

Records operations for automatic differentiation.

```python
with tf.GradientTape() as tape:
    prediction = model(x)
    loss = loss_function(y, prediction)
```

Used during model training.

---

# 27. `tf.keras.Sequential()`

Creates a neural network layer by layer.

```python
model = tf.keras.Sequential()
```

Best for simple feed-forward models.

---

# 28. `tf.keras.layers.Dense`

Creates a fully connected layer.

```python
tf.keras.layers.Dense(64)
```

---

# 29. `tf.keras.layers.Conv2D`

Creates a convolution layer.

```python
tf.keras.layers.Conv2D(
    filters=32,
    kernel_size=(3,3)
)
```

Used in image processing.

---

# 30. `tf.keras.layers.LSTM`

Creates an LSTM recurrent layer.

```python
tf.keras.layers.LSTM(128)
```

Used for:

* Time series
* NLP
* Forecasting

---

# 31. `model.compile()`

Configures the model for training.

```python
model.compile(
    optimizer="adam",
    loss="categorical_crossentropy",
    metrics=["accuracy"]
)
```

Specifies:

* Optimizer
* Loss function
* Metrics

---

# 32. `model.fit()`

Trains the model.

```python
model.fit(
    X_train,
    y_train,
    epochs=10
)
```

---

# 33. `model.evaluate()`

Evaluates model performance.

```python
loss, accuracy = model.evaluate(
    X_test,
    y_test
)
```

---

# 34. `model.predict()`

Generates predictions.

```python
predictions = model.predict(new_data)
```

---

# 35. `model.save()`

Saves a trained model.

```python
model.save("model.keras")
```

---

# 36. `tf.keras.models.load_model()`

Loads a saved model.

```python
model = tf.keras.models.load_model(
    "model.keras"
)
```

---

# 37. `tf.data.Dataset`

Creates efficient input pipelines.

```python
dataset = tf.data.Dataset.from_tensor_slices(
    (features, labels)
)
```

Supports:

* batching
* shuffling
* caching
* prefetching

---

# 38. `batch()`

Groups records.

```python
dataset = dataset.batch(32)
```

---

# 39. `shuffle()`

Randomizes training data.

```python
dataset = dataset.shuffle(1000)
```

---

# 40. `prefetch()`

Loads future batches while the model trains.

```python
dataset = dataset.prefetch(
    tf.data.AUTOTUNE
)
```

Improves training performance.

---

# Typical TensorFlow Workflow

```text
Load Data
     ↓
Preprocess Data
     ↓
Create Dataset
     ↓
Build Model
     ↓
Compile Model
     ↓
Train Model
     ↓
Evaluate Model
     ↓
Predict
     ↓
Save & Deploy
```

---

# Common Interview Use Cases

| Component          | Purpose                     |
| ------------------ | --------------------------- |
| `tf.constant()`    | Create immutable tensors    |
| `tf.Variable()`    | Trainable parameters        |
| `tf.matmul()`      | Matrix multiplication       |
| `tf.reshape()`     | Change tensor shape         |
| `tf.cast()`        | Convert data types          |
| `tf.reduce_mean()` | Average values              |
| `tf.argmax()`      | Predicted class index       |
| `GradientTape`     | Automatic differentiation   |
| `Sequential`       | Build neural networks       |
| `Dense`            | Fully connected layers      |
| `Conv2D`           | Image recognition           |
| `LSTM`             | Sequential/time-series data |
| `compile()`        | Configure training          |
| `fit()`            | Train the model             |
| `evaluate()`       | Measure performance         |
| `predict()`        | Generate predictions        |
| `save()`           | Persist trained model       |
| `tf.data.Dataset`  | Efficient data pipeline     |

---

# Senior-Level Interview Answer

> **TensorFlow is a production-grade machine learning framework used to build, train, and deploy deep learning models. At its core, it operates on tensors and provides automatic differentiation, GPU/TPU acceleration, and scalable data pipelines. In production, I typically use `tf.data.Dataset` for efficient input processing, `tf.keras` APIs to build and train models, `GradientTape` for custom training logic when needed, and model serialization for deployment. TensorFlow is commonly used for computer vision, natural language processing, recommendation engines, anomaly detection, and time-series forecasting.**
