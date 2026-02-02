`Quantum.py` is a quantum module designed to simulate quantum systems and complex matrix computations.
Although purely Python, this module is capable of performing advanced mathematical operations—such as Fourier transforms, quantum evolution, Grover Search, and VQE.

---

## 2. Quan Class
### 2.1 definition
`Quan` is just a function to perform calculations for the `Quantum` function.

---

### 2.2 APIs
| Functions | Description |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| `add`, `subtract`, `multiply`, `divide` | Basic arithmetic operations |
| `trigono(x)` | Returns sin, cos, tan, asin, acos, atan |
| `logex(x)` | Logarithms and exponentiations |
| `matrix_mul(A,B)` | Matrix multiplication |
| `matrix_inv(A)` | Matrix inverse using Gauss-Jordan elimination |
| `Erelative(m)` | Einstein's formula ( E = mc^2 ) |
| `Ephoton(f)` | Photon energy formula (E = hf) |
| `compress_array(x)` | Compression of numeric values ​​using stable logarithms |
| `build_C_vector()` | Constructing a composite complex vector of various physical parameters |
| `build_H_eff()` | Constructing an effective Hamiltonian for quantum energy |
| `expm_apply(H,state)` | Applying matrix exponential ( e^{iH} ) to a quantum state |
| `qft(state)` | Manual Quantum Fourier Transform |
| `normalize(state)` | Normalizing quantum amplitudes |
| `measure_topk(state)` | Measures the highest probability (Top-K measurement) |

## 3. Quantum Class
### 3.1 Definition
Simulate an n-qubit quantum system using a full state vector of size 2^n.

---

### 3.2 Initialization
* The state starts as `|000…0⟩`
* Vector length = `2^qubit_size`

---

### 3.3 Gate Operations
| Gate | Method |
| -------- | ----------------- |
| Hadamard | `hadamard(i)` |
| Pauli‑X | `pauli_x(i)` |
| Pauli‑Y | `pauli_y(i)` |
| Pauli‑Z | `pauli_z(i)` |
| CNOT | `cnot(state,c,t)` |

example code:
```python
q.hadamard(0)
q.pauli_x(1)
```

---

### 4.5 Attachment
```python
q.entangle(0,1)
```

Logical entanglement enforces correlated measurement bits.

---

### 4.6 Measurement
```python
result = q.measure(top_k=5)
```
Returns:
| Fields | Meaning |
| --------------- | -------------------------- |
| `result` | Sampled bitstring |
| `probabilities` | Full probability vector |
| `top_k` | Highest‑probability states |
| `stats` | mean/median/stdev |

---

### 4.7 State Evolution

| Method | Description |
| ----------------------------------- | --------------------------- |
| `unstable_multiprocessing_update()` | Parallel noisy evolution |
| `evolve_absolute()` | Hamiltonian‑based evolution |

---

### 4.8 Quantum Algorithms
| Algorithm | Method |
| ---------------- | ------------------- |
| Grover Search | `grover(oracle)` |
| Short (simulated) | `shor(n)` |
| QFT | `qfts()` |
| VQE | `vqe(cost_fn)` |
| QAOA | `qaoa(hamiltonian)` |

---

### 4.9 Diagnostics & Utilities
| Method | Purpose |
| ------------------- | -------------------- |
| `debug_state()` | Log readable summary |
| `compact_summary()` | Dict summary |
| `helper_memory()` | Memory snapshots |
| `summary()` | System overview |
| `reset()` | Reset quantum state |

---
