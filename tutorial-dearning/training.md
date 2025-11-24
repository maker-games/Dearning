## 1) **`test_model`` Function**

### Function
Used to:
* Test the AI ​​model after training
* Modify the prediction results to make them more stable (log → exp → pythagoras)
* Measure accuracy, loss, and prediction insight

### Brief Definition
```python
result = test_model(model, X, y, formula=None, verbose=True)
```

### Parameters
| Name | Type | Description |
| --------- | ----------------- | --------------------------------- |
| `model` | CustomAIModel | AI model to be tested |
| `X` | list[list[float]] | Input data |
| `y` | list[list[float]] | Label |
| `formula` | str | Transformation formula name notes |
| `verbose` | bool | Output to the console |

### Output
Dictionary contains:
* accuracy
* loss
* final_preds
* insight (mean/max/min prediction)
* formula_used

### How to Use
```python
result = test_model(model, X, y)
print(result["accuracy"])
```

---

# 2) **Class `transform`**
Transforms input data to:
* be more stable
* make the model easier to train
* be more informative

### Features
* log transform
* exp transform
* matrix projection
* averaging 3 methods (log + exp + linear)

### How to Use
```python
from dearning.training import transform
tr = transform()
X2 = tr.transform(X)
```

---

# 3) **Class `datal`**
Used to create a batch loader

---

## 3.1) **`datal.load()`**
Dearning's built-in dataset generator.

### Signature
```python
X, y = datal.load(task="classification", n_samples=500, n_features=4)
```

### Available Tasks
* `"classification"` → generates a 0/1 label based on the number of positive values
* `"regression"` → generates a numeric target

### Example Use
```python
from dearning.training import datal
X, y = datal.load(task="regression", n_samples=1000, n_features=5)
```

Because this function uses `@cached()`, if it is called twice with the same parameters, the loading time will be much shorter.

---

## 3.2) **`datal.loader()`**
Used for **mini-batch training**.

### Example of use
```python
loader = datal.loader(X, y, batch=32)
for Xb, yb in loader: 
print(len(Xb)) #32
```

---

#4) **Multi-Model `train()` Function**
The most important feature in this module.

### Key Features
* Multi-model training (parallel thread)
* Thread-safe
* Deterministic output
* Supports custom transforms (logekstrainnix)
* Automatic preprocessing
* Automatic evaluation

---

## 4.1) **How ​​to Use – Single Model**
```python
from dearning.training import train

model = CustomAIModel()
models, results = train(model)
```

---

## 4.2) **How ​​to Use – Multi-Model**
```python
m1 = AIModel()
m2 = AIModel()
m3 = AIModel()
models, results = train([m1, m2, m3], epochs=150)
```

---

## 4.3) Output Format
`results` is a list containing data from each model:

```python
[
("model_0", {"accuracy":0.89, ...}, 1.923), 
("model_1", {"accuracy":0.92, ...}, 2.174)
]
```

---

## 5) **Complete Usage Example**
```python
from dearning.training import datal, train, test_model
from dearning import CustomAIModel

# Create a model
m = CustomAIModel()
m.add_layer(16)
m.add_layer(8)
m.add_activation("relu")

# Create a dataset
X, y = datal.load(task="classification", n_samples=500)

# Trains
models, results = train(m, epochs=200)

# Test
score = test_model(models[0], X, y)
print(score)
```

---
