`AI_core` only provides **one main component**, namely **Converter**, a data conversion utility (text ↔ binary, byte units).

---

## 1. Converter Class
### Definition
`Converter` is a stateless utility for:
* Converting text ↔ ASCII binary
* Converting data units (B, KB, MB, etc.)

---

### 1.1 `Converter.text2binary`
```python
Converter.text2binary(data, *, to="binary", encoding="utf-8", sep=" ")
```
| Parameters | Type | Description |
| ---------- | --------------------- | ----------------------------- |
| `data` | `str` / `bytes` | Input data |
| `to` | `"binary"` / `"text"` | Conversion mode |
| `encoding` | `str` | Text encoding |
| `sep` | `str` | Binary separator (default space) |

#### Definition
`text2binary` is only for converting:
* text → ASCII binary
* ASCII binary → text

Example code:
```python
from dearning import Converter
print(Converter.text2binary("Hello", to="binary"))

# 01001000 01100101 01101100 01101100 01101111
print(Converter.text2binary("01001000 01100101 01101100 01101100 01101111", to="text")
# Hello
```

---

### 1.2 `Converter.unit`
```python
Converter.unit(value, to="B", (binary=True, precision=4)
```
| Parameters | Type | Description |
| ----------- | ----------------------- | ----------------------------- |
| `value` | `int` / `float` / `str` | Initial value (e.g., `"2MB"`) |
| `to` | `str` | Target unit |
| `binary` | `bool` | 1024 (True) or 1000 (False) |
| `precision` | `int` | Number of decimal places |

#### Definition
Converts data size between bytes.

example code:
```python
Converter.unit("2048B", to="KB")
# 2.0

Converter.unit("1GB", to="MB")
# 1024.0
```
