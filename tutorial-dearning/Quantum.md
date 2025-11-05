`Quantum.py` is a quantum module designed to simulate quantum systems and complex matrix computations.
Although purely Python, this module is capable of performing advanced mathematical operations—such as Fourier transforms, quantum evolution, Grover Search, and VQE.
efficiently through caching, multiprocessing, and high-precision manual algorithms.

---

## 1. General Module Structure

This module is divided into three main sections:

| Section | Description |
| ------------------- | ----------------------------------------------------------------------- |
| `linear_universe()` | Mathematical operations and data linearity based on classical physics |
| `Quan` | Collection of fundamental mathematical and quantum utilities |
| `Quantum` | High-level quantum system simulator with multiprocessing support |

---

## 2. `linear_universe(X)` Function

### Purpose

Converts input data (a list of numbers or a vector) into linear and nonlinear representations through a series of combined mathematical operations.
### Operations Performed

1. **Linear + Quadratic + Half-Linear**
( f(x) = x + x^2 + 0.5x )
2. **Cross Terms** – Interaction between features
3. **Simple and Multiple Linear Regression**
Used to find relationships between variables.
4. **Nonlinear Polynomial** – Term ( x^2 + 0.1x^3 )
5. **Linearization & Interpolation**
Normalizes and smooths data.
6. **Complex Mathematical Operations (sin, cos, log, exp, sqrt)**
Combined into a single result.

The final result is a new vector containing a mixture of linear and nonlinear expressions from each feature.

---

## 3. The `Quan` Class

### Purpose

Contains **basic quantum and mathematical functions** frequently used by the `Quantum` class.
All functions are decorated with `@cached` to store the same result for efficiency.

### Key Features

| Functions | Description |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| `add`, `subtract`, `multiply`, `divide` | Basic arithmetic operations |
| `trigono(x)` | Returns sin, cos, tan, asin, acos, atan |
| `logex(x)` | Logarithms and exponentiations |
| `matrix_mul(A,B)` | Matrix multiplication without NumPy |
| `matrix_inv(A)` | Matrix inverse using Gauss-Jordan elimination with `Decimal` (high precision) |
| `Erelative(m)` | Einstein's formula ( E = mc^2 ) |
| `Ephoton(f)` | Photon energy formula ( E = hf ) |
| `compress_array(x)` | Compression of numeric values ​​using stable logarithms |
| `build_C_vector()` | Constructing a composite complex vector of various physical parameters |
| `build_H_eff()` | Constructing an effective Hamiltonian for quantum energy |
| `expm_apply(H,state)` | Applying matrix exponential ( e^{iH} ) to a quantum state |
| `qft(state)` | Manual Quantum Fourier Transform |
| `normalize(state)` | Normalizing quantum amplitudes |
| `measure_topk(state)` | Measures the highest probability (Top-K measurement) |

All of these functions are the basis of quantum operations performed by the `Quantum` class.

---

## 4. The `Quantum` Class

### Purpose

Provides a high-level quantum simulator with:

* **Complex state vectors**
* **Quantum gates (Hadamard, Pauli, CNOT)**
* **Entanglement**
* **Grover Search & Variational Algorithms**
* **Multiprocessing simulation**
* **Model absolute quantum evolution**

---

### 4.1. Initialization

```python
q = Quantum(qubit_size=4, n_cores=2, seed=42, use_absolute=True)
```

| Parameters | Function |
| -------------- | ------------------------------------------------------- |
| `qubit_size` | Number of qubits (default 4 → 16 complex amplitudes) |
| `n_cores` | Number of CPU cores for multiprocessing |
| `seed` | Sets deterministic RNG |
| `use_absolute` | Enables “Absolute Quantum” mode (Dearning physical model) |

---

### 4.2. State Initialization

Every `Quantum` has a state vector:

```python
self._real, self._imag
```

which is of size (2^{n_qubit}) and starts in the ground state (|0...0\rangle).

---

### 4.3. Quantum Gates

Supports basic gate operations:

```python
q.hadamard(0)
q.pauli_x(1)
q.pauli_z(2)
```

| Gate | Description |
| ----------- | ---------------------------------- |
| `Hadamard` | Creates superposition |
| `Pauli-X` | Like NOT for classical bits |
| `Pauli-Y/Z` | Rotation about the Bloch sphere axis |
| `CNOT` | Controlled NOT gate (entanglement) |

---

### 4.4. Entanglement and Measurement

```python
q.entangle(0, 1)
result = q.measure(top_k=5)
```

The result is a dictionary:

```python
{
"result": "0101",
"probabilities": [...],
"top_k": [(idx, prob)],
"stats": {"mean": ..., "stdev": ...}
}
```

---

### 4.5. Absolute Quantum Evolution

```python
q.evolve_absolute(m_vec, f_vec, coupling_scale=1e-6)
```

Core mechanism of the **DAFE Quantum Engine (Absolute-Quan Evolution)**:

1. Calculate the relativistic and photonic energies.
2. Form the diagonal Hamiltonian.
3. Apply complex phase evolution ( e^{iH} ).
4. Fourier and variational transforms.
5. Amplitude compression and normalization.

---

### 4.6. Quantum Algorithms

| Method | Description |
| --------------- | ------------------------------------------------------- |
| `grover(mask)` | Grover search (amplitudes are inverted according to the oracle mask) |
| `shor(n)` | Simulating Shor's algorithm for factorization (mock) |
| `vqe(cost_fn)` | Variational Quantum Eigensolver* |
| `qaoa(H)` | Quantum Approximate Optimization Algorithm* |
| `qft()` | Quantum Fourier Transform |
| `debug_state()` | Displays a statistical summary of the state vector |

---

## 5. Optimization and Efficiency

* Caching (`@cached`)**
Avoids recalculation of deterministic functions.
* Multiprocessing Pool**
Breaks the state vector into chunks that are computed in parallel.
* Manual Normalization**
Does not rely on NumPy, but remains numerically stable.
* Decimal Precision (50 digits)**
Improves the accuracy of matrix inverse calculations.

---

## 6. Usage Example

```python
from dearning import Quantum, Quan

# Initialization
q = Quantum(qubit_size=3, n_cores=2)

# Apply some gates
q.hadamard(0)
q.pauli_x(1)
q.entangle(0, 2)

# Absolute-Quan Evolution
m = Quan.linspace(0.1, 0.5, 8)
f = Quan.linspace(1e12, 1e13, 8)
q.evolve_absolute(m_vec=m, f_vec=f)

# View results
q.debug_state(top_n=3)
```

---
