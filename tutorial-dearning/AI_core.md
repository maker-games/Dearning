`AI_core.py` is the **core security and analytics system** of the Dearning framework.

This module functions to:

* Track code and function activity (`CodeTracker`)
* Convert code or files to binary representation (`BinaryConverter`)
* Convert byte sizes to human units (`ByteConverter`)
* Provide **an automated DAFE (Dearning AI Front-End) protection layer** to detect anomalies, malware, or unusual behavior within the Dearning framework.

Overall, `AI_core` is the **internal security brain** of the entire framework.

---

## 1. General Structure

This module consists of four major parts:

1. **CodeTracker** → decorator logging system
2. **BinaryConverter** → string/file to binary conversion
3. **ByteConverter** → byte size conversion (KB, MB, GB, etc.)
4. **DafeGuard** → quantum/classical-based anomaly detection and monitoring system

---

## 2. Main Classes and Functions

### A. `CodeTracker`

This class allows you to automatically track function calls and their results using decorators.

####  Main functions:

| Function | Description |
| --------------------- | -------------------------------------------------------- |
| `__call__` | Allows the use of `@tracker` directly above a function |
| `log_function_call()` | Alternative to the `@tracker.log_function_call` decorator |
| `get()` | Retrieves all recorded logs |
| `clear()` | Clears all previous logs |

####  Usage example:

```python
from dearning import CodeTracker
tracker = CodeTracker()

@tracker.log_function_call
def add(a, b):
return a + b

print(add(2, 3))
print(tracker.get())
```

**Log output:**

```
['add() called.', ' add completed. → 5']
```

---

### B. `BinaryConverter`

Converts **Python code or text files to ASCII binary representation** and vice versa.

#### Main functions:

| Function | Description |
| ------------------------- | ------------------------------------ |
| `code2binary(code_str)` | Converts text to ASCII binary |
| `file2binary(file_path)` | Reads a file and converts it to binary |
| `binary2code(binary_str)` | Returns ASCII binary to a string |

#### Usage example:

```python
from dearning import BinaryConverter
bc = BinaryConverter()

binary = bc.code2binary("print('Hi')")
print(binary)
print(bc.binary2code(binary))
```

**Output:**

```
01110000 01110010 01101001 ...
print('Hi')
```

---

### C. `ByteConverter`

Utility class for converting byte sizes to a more readable format (KB, MB, GB, etc.).

#### Main functions:

| Function | Description |
| ----------------------------------- | ---------------------------------------------------- |
| `convert(size_bytes, precision=2)` | Convert bytes → human-sized string |
| `to_bytes(value, unit)` | Convert a specific value to bytes (e.g., 2 MB → 2097152 B) |

#### Example:

```python
from dearning import ByteConverter
print(ByteConverter.convert(10240)) # "10.00 KB"
print(ByteConverter.to_bytes(1, "MB")) # 1048576
```

---

### D. `DafeGuard`

This is the **core of Dearning's** security system.

`DafeGuard` runs automatically when Dearning is imported, and does the following:

* Monitors the framework size and structure
* Runs the *Quantum Engine* mode (if available)
* Detects anomalies with the **Mahalanobis Distance** algorithm
* Runs a background thread for monitoring
* Saves log results to `~/.dearning/dafe.log`
* Works even without NumPy and TensorFlow**

#### Main parameters:

| Parameter | Description |
| ------------------- | --------------------------------------------------------------- |
| `window_size` | Maximum amount of data stored in the detection window |
| `anomaly_threshold` | Anomaly score threshold |
| `telemetry_opt_in` | If `True`, compressed data will be stored locally as telemetry |
| `auto_instrument` | Experimental mode for tracking automated functions |

#### Internal features:

* `scan_environment()` → scans the Dearning directory and records the total size
* `get_directory_size()` → calculates the total folder size (KB)
* `record_event()` → inserts data into the Quantum & Mahalanobis system
* `_update_stats()` → calculates the mean and covariance
* `_background_worker()` → background thread for monitoring
* `_store_flagged()` → saves suspicious events to the `audit.jsonl` file
* `_upload_telemetry_summary()` → writes local telemetry data

#### Protection Mechanism:

This module can only be accessed **through `dearning`**, not directly.
If the user tries to `import AI_core` directly, an error will appear:

```
ImportError: ❌ DAFE should not be accessed directly.
```
---

## 3. Quantum Integration

If the `dearning.Quantum` module is available, DAFE will:

* Initialize the Quantum Engine
* ​​Execute the Hadamard Gate operation
* Measure the superposition probability result
* Feed the result to the anomaly detection system

If it fails, it automatically falls back to *classical* mode.

---

## Conclusion

| Components | Main Functions | Dependencies |
| ----------------- | ------------------------------------------ | --------------------- |
| `CodeTracker` | Track function calls | Pure Python |
| `BinaryConverter` | Convert code ↔ binary | Pure Python |
| `ByteConverter` | Convert bytes ↔ units of measure | Pure Python |
| `DafeGuard` | Automatic security system + Quantum support | Quantum.py (optional) |

AI_core.py is the heart of the Dearning security, integrity, and telemetry framework.
Without this module, Dearning would not have the following capabilities:

* detect suspicious behavior,
* track runtime activity,
* and adapt to the Quantum Engine.

---
