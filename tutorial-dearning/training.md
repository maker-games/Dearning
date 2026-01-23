`training` is the module that trains AI models.

## 1. `datal` Class
### Definition
`datal` is a **data utility namespace** (not an instantiable class) that provides:
* synthetic data generation,
* batch loading,
* feature transformation.

It is intended to quickly generate **training-ready datasets**.

---

## 1.1 `datal.load(...)`
```python
datal.load(task="classification", n_samples=500, n_features=4)
```

### Definition
Generates a normalized synthetic dataset for:
* classification
* regression

---

### Behavior
#### Classification

* Input `X`: Gaussian random values
* Label `y`:

`1` if **more than half** of the features are positive, otherwise `0`

#### Regression
* Input `X`: Gaussian random values
* Label `y`: `sum(features) + noise`

---

Example code:
```python
from dearning import datal
X, y = datal.load(task="regression", n_samples=100, n_features=3)
```
return value
```python
X, y
```
* `X`: `List[List[float]]`
* `y`: `List[List[float]]`

---

## 1.2 `datal.loader(...)`
```python
datal.loader(X, y, batch=32, shuffle=True)
```

### Definition
Generates a mini-batch of data for the training loop.

---

### Behavior
* Randomizes index (optional)
* Generates a tuple `(X_batch, y_batch)`
* **Does not** copy data unnecessarily

---

Example code:
```python
for Xb, yb in datal.loader(X, y, batch=16): print(Xb, yb)
```
---
## 3.3 `datal.transform(...)`
```python
datal.transform(X)
```

### Definition
Creates a **feature-enriched representation** of the raw input data.

### Applied Transformations
For each sample:
1. Logarithmic transformation
2. Exponential transformation

Example code:
```python
X_new = datal.transform(X)
```

---

## 2. `train(...)` function
```python
train(models, X, y, *, task=None, epochs=100, lr=0.01, batch_size=None, detail=False, log_interval=1)
```

### Definition
`train` is The **training controller** that trains this model:
* supports regression and classification,
* runs the model in parallel threads,
* reports loss, accuracy, and duration.

---

## 2.1 Model Requirements
Each model **must provide**:

```python
model.forward(X)
model._loss(y_pred, y_true)
model.train(X, y, epochs, learning_rate, batch_size, task)
```
Optional:
* `model.name` (for logging)

---

## 2.3 Loss & Accuracy
### Regression
* Loss: Mean Squared Error (MSE)
* Accuracy: not calculated

### Classification
* Loss: Cross entropy (expected)
* Accuracy: 
* Binary: threshold at 0.5 
* Multi-class: argmax

---

## 2.4 Logging Behavior
If `detail=True`:
```
epoch 1, model_0 | loss 0.123456
epoch 2, model_0 | loss 0.012345
...
[{'model': 'model_0', 'loss': 0.00388108, 'duration': 0.002694368362426758}]
```

If `detail=False` (default):
```
[{'model': 'model_0', 'loss': 23.38810804115213, 'duration': 0.002694368362426758}]
```
