The `training.py` module is an AI model training and testing module within the Learning framework.
It combines concepts from classical mathematics, logarithmic-exponential transformations, and quantum computing (Quantum Engine) integration to produce efficient, fully Python-based intelligent models.

---

## Main Functions and Structure
### 1. `test_model(model, X, y, formula=None, verbose=True)`
This function evaluates the model using an approach called Alogekstest-Phymetrix, a hybrid evaluation method based on logarithms, exponentials, and geometry.

**Working steps:**
1. Perform a forward pass of the model → generate predictions.
2. Perform log and exponential transformations to stabilize the predicted values.
3. Calculate the multidimensional Pythagorean distance** (measure the magnitude of the prediction).

4. Normalize to the range 0–1.
5. Run `evaluate_model()` from the `utils` module to measure accuracy and loss.
6. Generate additional insights such as the mean, maximum, and minimum predictions.

**Example usage:**
```python
from dearning.training import test_model
result = test_model(model, X, y)
print(result["insight"])
```

---

### 2. `class transform`
This class is a mathematical transformation system used to enrich data before the training process — known as **Logekstrainnix**.

**Main components:**
* `log` and `exp`: to expand the distribution of input values.
* `matrix mix`: combines data with `XᵀX` (the product of transposed matrices) to increase the correlation between features.
* `linear_universe`: Connects data to a quantum linear space (using the `linear_universe` function from the `Quantum` module).
* `Qtransform`: Performs the Hadamard Quantum Gate transform and performs a measurement (`measure`) for each qubit.

**Example usage:**
```python
from dearning.training import transform
logex = transform(use_quantum=True)
X_new = logex.transform(X)
```

**Benefits:**
* Strengthens feature representation before training.
* Improves model stability on nonlinear data.
* Enables quantum simulations without external libraries.

---

### 3. `load_dataset(task="classification", n_samples=500, n_features=4)`
Automatically builds a synthetic dataset.
Used for model testing, debugging, and benchmarking.

Mode:
* `"classification"` → binary data (0/1) based on the positive sign of the feature.
* `"regression"` → numeric data with Gaussian noise.

Additional features:
* Automatic normalization of each feature (mean 0, std 1).
* Data caching to speed up recall.

**Example:**
```python
X, y = load_dataset(task="regression", n_samples=300)
```

---

### 4. `data_loader(X, y, batch_size=32, shuffle=True)`
Mini-batch generator for incremental training.
Suitable for large models and small devices (smartphones/embedded devices).

**Advantages:**
* Supports `shuffle=True` to randomize data at each epoch.
* Efficient for repeated training without consuming a lot of memory.

**Example:**
```python
for Xb, yb in data_loader(X, y, batch_size=16):
model.train(Xb, yb, epochs=1)
```

---

### 5. `load_image_dataset(folder_path, size=(64,64), label_type="folder")`
A **pure Python image loader** function that reads `.ppm` files (P6 format).
Designed to eliminate the need for `Pillow` or `OpenCV`.

**Steps:**
1. Read the header and binary data of the image file.
2. Resize using *nearest neighbor*.
3. Normalize RGB 0–1.
4. Extract labels from the folder name.

**Example:**
```python
X, y = load_image_dataset("dataset_gambar/")
```

---

### 6. `fullevaluation(model, X, y, task)`
Provides comprehensive evaluation with automatic logging.
Uses `evaluate_model` and displays the results in an easy-to-read log format.

---

### 7. `train(model, task="classification", visualize=True, epochs=100, learning_rate=0.05, batch_size=32, use_logekstrainnix=False)`
The main function for training AI models.

**Highlight features:**
* Supports threaded training to prevent UI blocking.
* Integration with `Logekstrainnix` (pre-training transformation).
* Training time logging.
* Automatic evaluation at the end of the process.
* Supports optional `autograd` for manual backprop.

**Example usage:**
```python
from dearning.model import CustomAIModel
from dearning.training import train

model = CustomAIModel(layer_sizes=[4, 8, 1])
train(model, epochs=50, learning_rate=0.01, use_logekstrainnix=True)
```

---

### 8. `trainmultiple(n=3, input_size=4, output_size=1, task="classification", use_autograd=False, use_logekstrainnix=False)`
Train **multiple models in parallel** for experiments and ensemble learning.

**Features:**
* Create and train multiple `CustomAIModels` simultaneously.
* SetEach model has a default architecture of `[input, 16, 8, output]`.
* Can use `autograd` and `logekstrainnix`.

**Example:**
```python
models = trainmultiple(n=5, input_size=4, output_size=1)
```

---

## Integration with Other Modules

`training.py` interacts with several important modules:
| Module | Function | Description |
| --------- | ---------------------------------------------- | --------------------------------------------- |
| `model` | `CustomAIModel` | Provides the core architecture and training loop |
| `utils` | `preprocess_data`, `evaluate_model` | Normalization & evaluation metrics |
| `Quantum` | `Quantum`, `linear_universe` | Supports quantum simulations and transformations |
| `AI_core` | Internal logging and code tracing (optional) | |

---

## Complete Implementation Example

```python
from dearning.model import CustomAIModel
from dearning.training import train, test_model

# Create a simple model
model = CustomAIModel(layer_sizes=[4, 8, 1])

# Train the model
trained, result = train(model, epochs=50, learning_rate=0.01)

# Test the training results
X, y = [[0.2, 0.3, 0.5, 0.7]], [[1]]
test_result = test_model(trained, X, y)

print("Accuracy:", test_result["accuracy"])
```

---

## Core Scientific Concepts in This Module
* **Logekstrainnix** → hybrid mathematical + quantum preprocessing pipeline.
* Quantum Integration → Hadamard transform, probabilistic measurement.
* Pure Python Processing → All operations are performed without external libraries (optional cache decorator).

---

## 📄 Main Function Summary

| Functions / Classes | Purpose | Key Features |
| ---------------------- | --------------------------------- | --------------------------------------- |
| `test_model()` | Prediction result evaluation | Hybrid log-exp-geometry |
| `transform` | Quantum & mathematical transformation | Logekstrainnix Engine |
| `load_dataset()` | Synthetic dataset | Automatic normalization |
| `data_loader()` | Mini-batch generator | Shuffle & efficient |
| `load_image_dataset()` | `.ppm` image loader | No external libraries |
| `train()` | Main training | Multithreaded, quantum-ready |
| `trainmultiple()` | Multi-model training | Ensemble & parallel experiments |

---
