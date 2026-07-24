# PyTorch Library and Core Methods

**PyTorch** is an open-source machine learning and deep learning framework. It provides multidimensional tensors, automatic differentiation, neural-network layers, GPU acceleration, data pipelines, and model-training utilities. PyTorch supports both CPU and GPU computation. ([PyTorch Documentation][1])

```python
import torch
import torch.nn as nn
```

# Core Tensor Methods

## 1. `torch.tensor()`

Creates a tensor from Python data.

```python
numbers = torch.tensor([1, 2, 3])
```

A tensor is PyTorch’s primary data structure, similar to a NumPy array.

---

## 2. `torch.zeros()`

Creates a tensor filled with zeros.

```python
matrix = torch.zeros(2, 3)
```

Useful for initialization and placeholders.

---

## 3. `torch.ones()`

Creates a tensor filled with ones.

```python
matrix = torch.ones(2, 3)
```

---

## 4. `torch.full()`

Creates a tensor filled with a specified value.

```python
matrix = torch.full((2, 2), 10)
```

---

## 5. `torch.arange()`

Creates a sequence using a start, stop, and step.

```python
numbers = torch.arange(0, 10, 2)
```

Result:

```python
tensor([0, 2, 4, 6, 8])
```

---

## 6. `torch.linspace()`

Creates evenly spaced values between two endpoints.

```python
numbers = torch.linspace(0, 10, steps=5)
```

Useful for mathematical calculations and plotting.

---

## 7. `torch.rand()`

Creates random values from a uniform distribution between zero and one.

```python
values = torch.rand(3, 3)
```

---

## 8. `torch.randn()`

Creates random values from a normal distribution.

```python
values = torch.randn(3, 3)
```

Often used to initialize neural-network weights.

---

## 9. `torch.randint()`

Creates random integers.

```python
numbers = torch.randint(
    low=1,
    high=100,
    size=(5,)
)
```

---

## 10. `torch.manual_seed()`

Sets the random seed for more repeatable results.

```python
torch.manual_seed(42)
```

Complete reproducibility can still vary across PyTorch versions, hardware, and platforms. ([PyTorch Documentation][2])

---

# Tensor Properties

## 11. `.shape`

Returns the dimensions of a tensor.

```python
print(matrix.shape)
```

Example:

```text
torch.Size([2, 3])
```

---

## 12. `.size()`

Also returns tensor dimensions.

```python
print(matrix.size())
```

You can request one dimension:

```python
rows = matrix.size(0)
```

---

## 13. `.dim()`

Returns the number of dimensions.

```python
print(matrix.dim())
```

---

## 14. `.numel()`

Returns the total number of tensor elements.

```python
count = matrix.numel()
```

---

## 15. `.dtype`

Returns the tensor’s data type.

```python
print(matrix.dtype)
```

Common types include:

```python
torch.int32
torch.int64
torch.float32
torch.float64
torch.bool
```

---

## 16. `.device`

Shows whether the tensor is stored on the CPU, GPU, or another accelerator.

```python
print(matrix.device)
```

---

# Tensor Conversion and Device Methods

## 17. `.to()`

Moves a tensor to another device or converts its data type.

```python
tensor = tensor.to(torch.float32)
tensor = tensor.to("cuda")
```

PyTorch operations generally keep their results on the same device as their input tensors. ([PyTorch Documentation][3])

---

## 18. `.cpu()`

Moves a tensor to CPU memory.

```python
tensor = tensor.cpu()
```

---

## 19. `.cuda()`

Moves a tensor to an NVIDIA GPU.

```python
tensor = tensor.cuda()
```

A more portable approach is usually:

```python
device = torch.device(
    "cuda" if torch.cuda.is_available() else "cpu"
)

tensor = tensor.to(device)
```

---

## 20. `.numpy()`

Converts a CPU tensor into a NumPy array.

```python
array = tensor.numpy()
```

The tensor normally must be on the CPU and detached from gradient tracking.

```python
array = tensor.detach().cpu().numpy()
```

---

## 21. `torch.from_numpy()`

Creates a tensor from a NumPy array.

```python
import numpy as np

array = np.array([1, 2, 3])
tensor = torch.from_numpy(array)
```

The tensor and NumPy array may share memory.

---

# Shape and Reshaping Methods

## 22. `.reshape()`

Changes the tensor’s shape.

```python
tensor = torch.tensor([1, 2, 3, 4, 5, 6])

matrix = tensor.reshape(2, 3)
```

---

## 23. `.view()`

Returns a different view of the tensor using the same underlying data when possible.

```python
matrix = tensor.view(2, 3)
```

`reshape()` is usually safer when the tensor may not be contiguous.

---

## 24. `.flatten()`

Converts a multidimensional tensor into one dimension.

```python
flat = matrix.flatten()
```

---

## 25. `.squeeze()`

Removes dimensions whose size is one.

```python
tensor = torch.zeros(1, 3, 1)

result = tensor.squeeze()
```

---

## 26. `.unsqueeze()`

Adds a dimension of size one.

```python
tensor = torch.tensor([1, 2, 3])

batch = tensor.unsqueeze(0)
```

Commonly used to add a batch dimension.

---

## 27. `.transpose()`

Swaps two dimensions.

```python
result = matrix.transpose(0, 1)
```

---

## 28. `.permute()`

Reorders multiple tensor dimensions.

```python
image = torch.rand(32, 32, 3)

channels_first = image.permute(2, 0, 1)
```

Useful when converting image data from:

```text
height × width × channels
```

to:

```text
channels × height × width
```

---

# Mathematical Operations

## 29. `torch.sum()`

Calculates the sum.

```python
total = torch.sum(tensor)
```

By dimension:

```python
column_totals = torch.sum(matrix, dim=0)
```

---

## 30. `torch.mean()`

Calculates the average.

```python
average = torch.mean(tensor.float())
```

---

## 31. `torch.max()`

Returns the maximum value.

```python
maximum = torch.max(tensor)
```

---

## 32. `torch.min()`

Returns the minimum value.

```python
minimum = torch.min(tensor)
```

---

## 33. `torch.argmax()`

Returns the index of the largest value.

```python
predicted_class = torch.argmax(probabilities)
```

Commonly used for classification predictions.

---

## 34. `torch.argmin()`

Returns the index of the smallest value.

```python
index = torch.argmin(tensor)
```

---

## 35. `torch.std()`

Calculates standard deviation.

```python
standard_deviation = torch.std(tensor)
```

---

## 36. `torch.var()`

Calculates variance.

```python
variance = torch.var(tensor)
```

---

## 37. `torch.abs()`

Returns absolute values.

```python
result = torch.abs(
    torch.tensor([-5, 10, -20])
)
```

---

## 38. `torch.round()`

Rounds floating-point values.

```python
result = torch.round(
    torch.tensor([1.2, 2.7])
)
```

---

## 39. `torch.clamp()`

Restricts values to a minimum and maximum.

```python
result = torch.clamp(
    tensor,
    min=0,
    max=100
)
```

Similar to NumPy’s `clip()`.

---

# Matrix Operations

## 40. `torch.matmul()`

Performs matrix multiplication.

```python
result = torch.matmul(matrix_a, matrix_b)
```

The `@` operator can also be used:

```python
result = matrix_a @ matrix_b
```

---

## 41. `torch.mm()`

Performs matrix multiplication between two two-dimensional tensors.

```python
result = torch.mm(matrix_a, matrix_b)
```

---

## 42. `torch.bmm()`

Performs batched matrix multiplication.

```python
result = torch.bmm(batch_a, batch_b)
```

Useful when processing multiple matrices simultaneously.

---

## 43. `torch.cat()`

Combines tensors along an existing dimension.

```python
combined = torch.cat(
    [first, second],
    dim=0
)
```

---

## 44. `torch.stack()`

Combines tensors along a new dimension.

```python
combined = torch.stack(
    [first, second],
    dim=0
)
```

Difference:

* `cat()` uses an existing dimension.
* `stack()` creates a new dimension.

---

# Automatic Differentiation

## 45. `requires_grad=True`

Tells PyTorch to track operations for gradient calculation.

```python
weight = torch.tensor(
    2.0,
    requires_grad=True
)
```

---

## 46. `.backward()`

Calculates gradients using backpropagation.

```python
weight = torch.tensor(
    2.0,
    requires_grad=True
)

loss = weight ** 2
loss.backward()

print(weight.grad)
```

Result:

```text
tensor(4.)
```

---

## 47. `.grad`

Contains the calculated gradient.

```python
print(weight.grad)
```

---

## 48. `.requires_grad_()`

Enables or disables gradient tracking on an existing tensor.

```python
tensor.requires_grad_(True)
```

PyTorch uses this method to begin recording operations for automatic differentiation. ([PyTorch Documentation][4])

---

## 49. `.detach()`

Returns a tensor disconnected from the automatic-differentiation graph.

```python
result = tensor.detach()
```

The detached tensor shares underlying storage with the original tensor, so in-place changes should be handled carefully. ([PyTorch Documentation][5])

---

## 50. `torch.no_grad()`

Temporarily disables gradient calculation.

```python
with torch.no_grad():
    predictions = model(inputs)
```

Used during inference to reduce memory usage and overhead.

---

# Neural-Network Classes and Methods

## 51. `nn.Module`

Base class for PyTorch neural networks.

```python
class NeuralNetwork(nn.Module):
    def __init__(self):
        super().__init__()
```

Custom models should inherit from `nn.Module`.

---

## 52. `forward()`

Defines how input moves through the model.

```python
class NeuralNetwork(nn.Module):
    def __init__(self):
        super().__init__()
        self.layer = nn.Linear(10, 1)

    def forward(self, x):
        return self.layer(x)
```

Call the model directly:

```python
output = model(inputs)
```

Do not normally call `forward()` manually.

---

## 53. `nn.Linear`

Creates a fully connected neural-network layer.

```python
layer = nn.Linear(
    in_features=10,
    out_features=5
)
```

---

## 54. `nn.ReLU`

Applies the ReLU activation function.

```python
activation = nn.ReLU()
```

ReLU returns:

```text
max(0, x)
```

---

## 55. `nn.Sigmoid`

Produces values between zero and one.

```python
activation = nn.Sigmoid()
```

Commonly associated with binary classification outputs.

---

## 56. `nn.Softmax`

Converts logits into class probabilities.

```python
activation = nn.Softmax(dim=1)
```

---

## 57. `nn.Sequential`

Builds a model as an ordered sequence of layers.

```python
model = nn.Sequential(
    nn.Linear(10, 64),
    nn.ReLU(),
    nn.Linear(64, 1)
)
```

Useful for straightforward feed-forward models.

---

## 58. `nn.Conv2d`

Creates a two-dimensional convolution layer.

```python
layer = nn.Conv2d(
    in_channels=3,
    out_channels=32,
    kernel_size=3
)
```

Commonly used in image-processing models.

---

## 59. `nn.LSTM`

Creates a Long Short-Term Memory recurrent layer.

```python
layer = nn.LSTM(
    input_size=20,
    hidden_size=64,
    batch_first=True
)
```

Used for sequential and time-series data.

---

## 60. `nn.Dropout`

Randomly disables some neurons during training to reduce overfitting.

```python
layer = nn.Dropout(p=0.5)
```

---

# Loss Functions

## 61. `nn.CrossEntropyLoss`

Common loss function for multiclass classification.

```python
loss_function = nn.CrossEntropyLoss()
```

It expects raw logits, so do not normally apply softmax before it.

---

## 62. `nn.BCEWithLogitsLoss`

Common loss function for binary classification.

```python
loss_function = nn.BCEWithLogitsLoss()
```

It combines sigmoid behavior with binary cross-entropy in a numerically stable implementation.

---

## 63. `nn.MSELoss`

Calculates mean squared error.

```python
loss_function = nn.MSELoss()
```

Commonly used for regression.

---

## 64. `nn.L1Loss`

Calculates mean absolute error.

```python
loss_function = nn.L1Loss()
```

It can be less sensitive to extreme outliers than MSE.

---

# Optimizers

The `torch.optim` package contains optimization algorithms that update model parameters using calculated gradients. ([PyTorch Documentation][6])

## 65. `torch.optim.SGD`

Stochastic gradient descent optimizer.

```python
optimizer = torch.optim.SGD(
    model.parameters(),
    lr=0.01,
    momentum=0.9
)
```

---

## 66. `torch.optim.Adam`

Adaptive optimizer commonly used as a strong default.

```python
optimizer = torch.optim.Adam(
    model.parameters(),
    lr=0.001
)
```

---

## 67. `optimizer.zero_grad()`

Clears previously accumulated gradients.

```python
optimizer.zero_grad()
```

PyTorch gradients accumulate by default.

---

## 68. `optimizer.step()`

Updates model parameters using their gradients.

```python
optimizer.step()
```

---

# Model Modes

## 69. `model.train()`

Places the model in training mode.

```python
model.train()
```

This affects layers such as dropout and batch normalization.

---

## 70. `model.eval()`

Places the model in evaluation mode.

```python
model.eval()
```

Use it before validation or inference.

---

# Data Loading

## 71. `Dataset`

Represents a collection of training or testing samples.

```python
from torch.utils.data import Dataset
```

A custom map-style dataset normally implements:

```python
__len__()
__getitem__()
```

---

## 72. `DataLoader`

Loads data in batches and supports shuffling and multiprocessing.

```python
from torch.utils.data import DataLoader

loader = DataLoader(
    dataset,
    batch_size=32,
    shuffle=True,
    num_workers=4
)
```

`DataLoader` is an iterable over a dataset and supports batching, custom ordering, multiprocessing, and memory pinning. ([PyTorch Documentation][7])

---

# Saving and Loading Models

## 73. `model.state_dict()`

Returns the model’s learned parameters.

```python
weights = model.state_dict()
```

---

## 74. `torch.save()`

Saves tensors, model parameters, or checkpoints.

```python
torch.save(
    model.state_dict(),
    "model.pt"
)
```

The `.pt` extension is a common PyTorch convention. ([PyTorch Documentation][8])

---

## 75. `torch.load()`

Loads saved data.

```python
state = torch.load(
    "model.pt",
    map_location="cpu",
    weights_only=True
)
```

Using `map_location="cpu"` allows GPU-trained weights to be loaded into CPU memory. ([PyTorch Documentation][9])

---

## 76. `model.load_state_dict()`

Loads parameters into a model.

```python
model.load_state_dict(state)
```

A common complete pattern is:

```python
model = NeuralNetwork()

state = torch.load(
    "model.pt",
    map_location="cpu",
    weights_only=True
)

model.load_state_dict(state)
model.eval()
```

---

# Typical Training Loop

```python
model.train()

for features, labels in train_loader:
    features = features.to(device)
    labels = labels.to(device)

    optimizer.zero_grad()

    predictions = model(features)

    loss = loss_function(
        predictions,
        labels
    )

    loss.backward()

    optimizer.step()
```

The main steps are:

```text
Load batch
   ↓
Run forward pass
   ↓
Calculate loss
   ↓
Clear old gradients
   ↓
Run backpropagation
   ↓
Update model parameters
```

# Core Methods Cheat Sheet

| Method or class         | Purpose                                |
| ----------------------- | -------------------------------------- |
| `torch.tensor()`        | Creates a tensor                       |
| `torch.zeros()`         | Creates zero-filled tensors            |
| `torch.rand()`          | Creates uniform random tensors         |
| `torch.randn()`         | Creates normally distributed tensors   |
| `.reshape()`            | Changes tensor shape                   |
| `.to()`                 | Moves or converts a tensor             |
| `torch.matmul()`        | Performs matrix multiplication         |
| `torch.sum()`           | Calculates totals                      |
| `torch.argmax()`        | Finds the largest-value index          |
| `requires_grad`         | Enables gradient tracking              |
| `.backward()`           | Calculates gradients                   |
| `.detach()`             | Disconnects from the gradient graph    |
| `torch.no_grad()`       | Disables gradient tracking temporarily |
| `nn.Module`             | Base class for neural networks         |
| `nn.Linear`             | Creates a fully connected layer        |
| `nn.Sequential`         | Organizes layers sequentially          |
| `model.train()`         | Enables training behavior              |
| `model.eval()`          | Enables evaluation behavior            |
| `DataLoader`            | Loads and batches data                 |
| `optimizer.zero_grad()` | Clears old gradients                   |
| `optimizer.step()`      | Updates model parameters               |
| `torch.save()`          | Saves model data                       |
| `torch.load()`          | Loads model data                       |

# Senior-Level Interview Answer

> PyTorch is a tensor-computing and deep-learning framework with CPU and GPU acceleration. Its core abstraction is the tensor, while autograd records operations and calculates gradients for backpropagation. I use `nn.Module` to define models, `DataLoader` to create scalable input pipelines, loss functions to measure error, and optimizers such as Adam or SGD to update model parameters. A typical training cycle is forward pass, loss calculation, `zero_grad()`, `backward()`, and `step()`. For production, I save and load model state dictionaries, control training and inference behavior with `train()` and `eval()`, and explicitly manage devices, batching, reproducibility, and monitoring.

[1]: https://docs.pytorch.org/?utm_source=chatgpt.com "PyTorch documentation — PyTorch 2.13 documentation"
[2]: https://docs.pytorch.org/docs/stable/notes/randomness.html?utm_source=chatgpt.com "Reproducibility — PyTorch 2.12 documentation"
[3]: https://docs.pytorch.org/docs/stable/notes/cuda.html?utm_source=chatgpt.com "CUDA semantics — PyTorch 2.13 documentation"
[4]: https://docs.pytorch.org/docs/stable/generated/torch.Tensor.requires_grad_.html?utm_source=chatgpt.com "torch.Tensor.requires_grad_ — PyTorch 2.12 documentation"
[5]: https://docs.pytorch.org/docs/stable/generated/torch.Tensor.detach.html?utm_source=chatgpt.com "torch.Tensor.detach — PyTorch 2.13 documentation"
[6]: https://docs.pytorch.org/docs/stable/optim.html?utm_source=chatgpt.com "torch.optim — PyTorch 2.12 documentation"
[7]: https://docs.pytorch.org/docs/stable/data.html?utm_source=chatgpt.com "torch.utils.data"
[8]: https://docs.pytorch.org/docs/stable/generated/torch.save.html?utm_source=chatgpt.com "torch.save — PyTorch 2.12 documentation"
[9]: https://docs.pytorch.org/docs/stable/generated/torch.load.html?utm_source=chatgpt.com "torch.load — PyTorch 2.12 documentation"
