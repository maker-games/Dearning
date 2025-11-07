This module serves as a tool for Dearning. It assists with caching, scaling, preprocessing, model evaluation, memory storage, and conversion between data formats (adapters).
All functions here are **written in pure Python**, with no dependencies on external libraries.

---

## 1. Main Features of the `utils.py` Module

| Category | Features |
| ------------------------ | ----------------------------------------------------------------- |
| **Optimization & Caching** | `@cached` decorator with JIT and Precision modes |
| **Data Preprocessing** | Manual normalization & scaling, parallel preprocessing |
| **Model Evaluation** | Evaluation of training results (MSE, R², Accuracy, F1, etc.) |
| **Memory System (DOMM)** | Saving models and experiences to `.dm`, `.db`, ​​and `.json` files |
| **Data System Adapter** | Convert to JSON, CSV, NumPy, Pandas, Polars, PyArrow, Pillow |
| **GPU Adapter** | GPU, OpenCL, CUDA detection, and simple GPU execution planning |

---

## 2. Caching Decorator: `@cached()`

### Function:
Caches the results of a function calculation to make subsequent calls **faster**.

### Mode:
* `"precision"` → focuses on the precision of numerical results
* `"jit"` → Just-In-Time pseudo-optimizer (dynamically reorders Python code)
* default → normal cache (simple LRU)

### Parameters:
```python
@cached(maxsize=128, threshold=64, mode="precision")
def function(x):
...
```
| Parameters | Function |
| ----------- | ------------------------------------------------------ |
| `maxsize` | Limit on the number of cache entries |
| `threshold` | Limit on the size of large data that does not need to be fully hashed |
| `mode` | Choice of `"precision"`, `"jit"`, or `None` |

### Example:
```python
from dearning.utils import cached
@cached("precision")
def hitung(data):
return [x * 0.1 for x in data]

print(hitung([10, 20, 30]))
```

The `"precision"` mode automatically sums numeric results for greater stability.

---

## 3. Scaling Data: `scale_data(data)`
Performs **standardization of data (mean=0, std=1)** without external libraries like `sklearn`.

### Example:
```python
data = [[1, 2], [3, 4], [5, 6]]
scaled = scale_data(data)
```
The output is the normalized data per column.

---

## 4. Data Preprocessing: `preprocess_data()`
An advanced version of `scale_data` — capable of parallelization and optimizer integration (SGD, Adam, RMSprop, Momentum).

### Example:
```python
scaled, (w_new, b_new, state) = preprocess_data(
data, optimizer_args=(w, b, grad_w, grad_b, 0, "adam", 0.01))
)
```

### Features:
* Automatically parallelizes large data processing (>1000 samples)
* Supports four optimizer algorithms:
* `SGD`
* `Momentum`
* `RMSProp`
* `Adam`
* Saves `state` at each iteration for *adaptive learning*

---

## 5. Model Evaluation: `evaluate_model()`

Evaluates model performance after training, both regression and classification.

### Example:
```python
from dearning.utils import evaluate_model
result = evaluate_model(model, X_test, y_test)
print(result)
```

### Features:
* Automatic task detection (`classification`/`regression`)
* Calculates:
* MSE (Mean Squared Error)
* R² Score
* Mean Absolute Error
* Accuracy, Precision, Recall, F1
* Confusion Matrix
* Can return optimizer results if `optimizer_args` is included

---

## 6. Memory Manager: `DOMM`

### Function:
The `DOMM` (Dearning Object Memory Manager) class manages the storage of models and experiences (reinforcement learning) to disk.

### Files used:
| File | Contents |
| ------- | ------------------------------------------------ |
| `.db` | Models stored via `shelve` |
| `.json` | Experience history (`state`, `action`, `reward`) |
| `.dm` ​​| Main combined file (model + experience) |

### Example:
```python
mem = DOMM("MyModel")
mem.save_model("weights", {"W": [1, 2, 3]})
print(mem.load_model("weights"))
```

### DOMM Features:
* Save and load models via `save_model` / `load_model`
* Add experiences via `add_experience`
* Total file size limit of 20 MB
* Search for experiences with `search_experience(min_reward=...)`
* Create new `.dm` ​​files via `create_dm()`

---

## 7. Adapter: `Adapter` Class
Used for compatibility with cross-format data conversion and integration with external ecosystems or libraries.
#### Base Format:
```python
Adapter.json(obj)
Adapter.csv(list_data)
```

#### numpy dan scipy:
```python
Adapter.numpy(data)
Adapter.scipyspar(data)
```

#### pandas, polars, dan pyarrow:
```python
Adapter.pandas(data)
Adapter.polars(data)
Adapter.pyarrow(data)
```

#### pillow dan librosa:
```python
Adapter.pillow("image.png")
Adapter.librosa("audio.wav")
```

#### pygame:
```python
Adapter.pygame(data)
```

---

## 8. GPU Interface: `Adapter.GPU`
Offers support for GPU detection and OpenCL introspection without additional libraries, but is still under development.

### Features:
* `gpu_available()` → Check GPU/CUDA availability
* `gpu_info()` → Get a list of OpenCL platforms and devices
* `opencl_simple_vector_add()` → Simple vector execution plan on the GPU

### Example:
```python
if Adapter.GPU.gpu_available():
info = Adapter.GPU.gpu_info()
print(json.dumps(info, indent=4))
```

Example output:
```json
{
"opencl": true,
"platforms": [
{
"name": "NVIDIA CUDA",
"version": "OpenCL 3.0",
"devices": [
{"name": "RTX 3060", "compute_units": 28, "global_mem_bytes": 4294967296}
]
}
]
}
```

---

## 9. Integration in Dearning

The `utils.py` module is the foundation upon which the entire Dearning system rests:

* `@cached` is used in many tensor and AI model operations
* `scale_data` and `preprocess_data` are used in the `training.py` module
* `evaluate_model` is used for validation and result metrics
* `DOMM` is used by `service.py` for automatic model caching
* `Adapter` connects Dearning to external libraries if available

---

## Conclusion

The `utils.py` module is Dearning's functional toolkit that helps:
* **Performance** (through caching and pseudo-JIT)
* **Independence** (pure Python, no external dependencies)
* **Compatibility** (with external systems like NumPy, Pandas, GPUs)
* **Storage** (via DOMM memory) system)

---
