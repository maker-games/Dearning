`utils` serves as a toolkit for Dearning. This module assists with caching, scaling, preprocessing, model evaluation, memory storage, and conversion between data formats (adapters).

## 1. `cached`
```python
cached(self, *decorators, maxsize=128, mode=None, debug_modes=None, force_debug=False)
```
| Parameters | Function |
| ------------------ | -------------------------------------------- |
| `maxsize` | Maximum number of cache entries (default 128) |
| `mode="precision"` | Stable numeric summation (Kahan summation) |
| `mode="jit"` | Pseudo-JIT wrapper (fast call reuse) |
| `debug_modes` | Enable time/memory/error logging |
| `force_debug` | Force logging even below threshold |

### 1.1 Definition
Caching function results based on arguments is used for:
* speeding up repeated calls
* performance debugging
* high-precision numerical experiments

example code:
```python
@cached()
def fib(n):
if n < 2: return n
return fib(n-1) + fib(n-2)
```

---

### 1.2 Debugging with `cached.debug
Debug Mode
* `"time"` → log if > 0.7 seconds
* `"memory"` → log if the cache grows ≥ 512 bytes
* `"error"` → show hidden traceback
* `jdi` → force logging

example code:
```python
@cached.debug("time", "memory")
def heavy(x):
return sum(i*i for i in range(x))

# Force debugging jdi
@cached.debug(cached.jdi("time", "memory"))
def f(x): ...
```

---

## 2. `preprocess_data`
``` python
preprocess_data(data, n_jobs=-1, optimizer_args=None)`
```
### 2.1 Definition
The `preprocess_data` function transforms raw data into safer and more consistent data. It also supports:
* `sgd`
* `momentum`
* `rmsprop`
* `adam`

Example code:

Scaling only
```python
X = preprocess_data(X)
```

Scaling + optimizer step
```python
X_scaled, (w_new, b_new, state) = preprocess_data(X, optimizer_args=(w, b, grad_w, grad_b, layer_idx, "adam", 0.001, 0.9, 0.999, 1e-8, state))
```

---

## 3. `evaluate_model`
```python
evaluate_model(model, data, labels=None, task=None, threshold=0.5, optimizer_args=None)
```
### 3.1 Definition
Model Evaluation

If `task=None`, then:
* `{0,1}` → Classification
* Additional → Regression

example code:

Regression:
```python
from dearning import CustomAIModel, Dense, Activation, evaluate_model
data = [[0.1, 0.2], [0.2, 0.3], [0.3, 0.4]]
labels = [[0.3], [0.5], [0.7]]
model = CustomAIModel(loss="mse", name="model")
b1 = model.connect(Dense(2, 1), data)
b1 = model.connect(Activation("relu"), b1)
print(evaluate_model(model, data, labels, task="regression"))
```
Classification:
```python
from dearning import CustomAIModel, Dense, Activation, evaluate_model
model = CustomAIModel(loss="cross_entropy", name="model")
data = [[0.1, 0.2], [0.8, 0.9], [0.4, 0.5]]
labels = [[0], [1], [0]]
b2 = model.connect(Dense(2, 1), data)
b2 = model.connect(Activation("sigmoid"), b2)
print(evaluate_model(model, data, labels, task="classification"))
```

---

## 4. `Adapter` — Interoperability Layer
### 4.1 Definition
Converts data to another library format.

```python
Adapter.numpy(data)
Adapter.pandas(data)
Adapter.polars(data)
Adapter.scipyspar(data)
Adapter.pyarrow(data)
Adapter.librosa("audio.wav")
Adapter.pillow("image.png")
```
If the library is not available → fallback to the original input.

---

## 4.2 GPU
⚠️ still under development
### Check GPU
```python
Adapter.GPUD.gpu_available()
Adapter.GPUD.gpu_info()
```

Example output:
```python
{"vendors": ["NVIDIA"], "devices": ["RTX 3060"], "api": {"cuda": True, "opencl": True}}
```

---

### OpenCL Vector Addition
```python
Adapter.GPUD.opencl_vector_add([1,2,3],[4,5,6])
# → [5.0, 7.0, 9.0]
```

---
