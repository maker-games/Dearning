`service.py` to **transfer model or code files** between machines using **HTTP/HTTPS** with:
* password-based authentication,
* optional TLS (SSL),
* background threaded server,
* client-side upload (`post`) and download (`load`),

---
## 1. Certificate Generator — `DGS`
```python
DGS(cert_dir: str, force_path_required=True)
```
Automatically generates **self-signed TLS certificates** using `openssl`.
* Checks for existing `server.crt` and `server.key`
* If missing → generates new certificate
* Uses RSA-2048, valid for 365 days

example code:
```python
cert, key = DGS("dm_models/certificates")
```

Returns
```python
(cert_path, key_path)
```
⚠ Requires `openssl` installed on the system.

---

## 2. Running the Server — `run_server`
```python
run_server(host="0.0.0.0", port=8443, password="dearning_secure", sslc=False, mode="internal", threaded=True, daemon=True)
```

### 2.1 Modes
| Mode       | Description              |
| ---------- | ------------------------ |
| `internal` | Bind to provided host    |
| `wifi`     | Auto-detect LAN IP       |
| `cable`    | USB tethering IP         |
| `mesh`     | Peer list (experimental) |
---
### 2.2 Control
* `sslc=True` → HTTPS
* Auto certificate via `DGS`
* `threaded=True` → non-blocking
* Returns background `Thread`
---

example code:
```python
run_server(host="127.0.0.1", port=8080, password="1234", sslc=False)
```
---

## 3. Upload — `post`
```python
post(host, port, model_path, password, sslc=False, timeout=60)
```
Upload one or multiple files to a running server.

example code:
```python
post(host="127.0.0.1", port=8080, model_path="model.py",  password="1234")
```

Multiple files:
```python
post(host="127.0.0.1", port=8080, model_path=["a.py", "b.py"], password="1234")
```

---

## 4. Download — `load`
```python
load(host, port, filename, password, sslc=False, timeout=60, mode="code", save_path=None)
```

### 4.1 Modes
| Mode   | Behavior                       |
| ------ | ------------------------------ |
| `code` | Return file contents as string |
| `file` | Save to disk                   |

---

example code:

code
```python
code = load(host="127.0.0.1", port=8080, filename="model.py", password="1234")
print(code)
```

file
```python
load(host="127.0.0.1", port=8080, filename="model.py", password="1234", mode="file", save_path="./downloaded")
```

---
