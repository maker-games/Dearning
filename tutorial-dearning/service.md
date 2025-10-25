## Quick Summary

`serving.py` provides HTTP/HTTPS utilities for:

* Runs a small REST server to **import** and **load** model files (`.dm`/.py or other formats) to/from the `MODEL_DIR` folder.
* Automatically generates self-signed TLS certificates (fallback to dummy ones if `openssl` is unavailable).
* Provides client helpers (`post`, `load`, async wrappers) for sending/retrieving models to/from the server.

* Internal cache for small models for faster access.
* Different network modes: `internal` (default), `wifi`, `wired`, `mesh`.

The module is designed to be compact, suitable for use on limited devices (e.g., smartphones) and local environments.

---

## Location & Configuration

**Main constants** (can be controlled via environment variables):

* `MODEL_DIR` — the folder where the models are stored. Default: `dm_models` or `DEARNING_MODEL_DIR`.
* `DEFAULT_PASSWORD` — the default password for the API endpoint. Default: env `DEARNING_PASSWORD` or `dearning_secure`.
* `DEFAULT_HOST` — the default server host (0.0.0.0).
* `DEFAULT_PORT` — the default port (8443) or env `DEARNING_PORT`.

> When running the server in a live environment: always set `DEARNING_PASSWORD` to a strong value.

---

## Certificate Generator: `_auto_generate_cert(cert_dir: Path)`

This function is responsible for generating a self-signed TLS certificate (**server.crt**) and private key (**server.key**) if they don't already exist.

* Try calling `openssl` (calling `subprocess.run([...])`) with `rsa:2048`, valid for 365 days.
* If `openssl` fails/is small, the function creates a "dummy" file containing placeholders (NOT secure) — still allowing the server to run.

**Notes & recommendations:**

* For production, use a valid certificate (Let's Encrypt or an internal CA certificate).
* Dummy certs are **not secure**; do not use them on the public internet.

---

## Model Cache

* `_MAX_CACHE_BYTES` — size limit (2 MiB) for cached model contents.
* `_model_cache` — dict mapping `filename` -> `bytes` for small models.

The cache makes `load_model` fast if the model has been loaded before.

---

## Background task: `domm_background_task` and loop starter

* `domm_background_task(interval=5.0)` — asynchronous coroutine that runs continuously, clearing the cache if it becomes too large (e.g., `len(_model_cache) > 50`).
* `start_domm_background_loop()` — executes the async event loop on a daemon thread to prevent server blocking.

**Note:** Simple loop — if you need to collect statistics or upload telemetry, add it here.

---

## `DearningHandler` — HTTP request handler

`DearningHandler` is a subclass of `http.server.BaseHTTPRequestHandler`. Its main features are:

### Internal helpers

* `_read_json()` — reads the HTTP body based on `Content-Length` and parses it into JSON. Returns a `dict` or `None`.
* `_send_json(code, obj)` — returns a JSON response with an HTTP status code.

### `do_POST()`

Accepts only POST requests. Flow:

1. Read JSON via `_read_json()`.
2. Check that `password` from the payload matches `server.cloud_password` (or `DEFAULT_PASSWORD`). If incorrect → 403.
3. Route by path:

* `/import_model` → `_handle_import_model`
* `/load_model` → `_handle_load_model`
* else → 404

> **Security:** Password verification comes from the JSON payload — it doesn't use the Authorization header. This is sufficient for LAN/local use but is easily exposed if public.

### `_handle_import_model(self, data)`

Expected payload:

```
{
"password": "...",
"filename": "model.dm",
"content_b64": "(base64-encoded bytes)"
}
```

Steps:

* Get `filename` and `content_b64`.
* Decode base64 to bytes; Write to the file `MODEL_DIR/safe_name` via a temporary `.tmp` file, then use `os.replace()` (atomic replace).
* If the file is small (<= `_MAX_CACHE_BYTES`), store the bytes in `_model_cache`.
* Invert the `imported` status and file size.

**File path security:** Use `os.path.basename()` to prevent path traversal.

### `_handle_load_model(self, data)`

Payload:

```
{"password": "...", "filename": "model.dm"}
```

Steps:

* Ensure the file exists in `MODEL_DIR`.
* If it exists in `_model_cache`, use the cache; otherwise, read from disk and cache if it is small.
* Send back `content_b64` containing the base64 bytes of the file.

Note: Model submission is done in a single JSON response — large sizes will consume memory and bandwidth.

### `log_message(self, *a)`

Overridden to disable default logging to stderr (non-verbose handler).

---

## Server Runner: `run_server(...)`

Main function for starting the server. Parameters:

* `host, port, password`
* `sslc` (bool) — whether to use TLS/SSL (default True)
* `mode` — `internal` | `wifi` | `wired` | `mesh` (see details)(see below)
* `threaded` (bool) — run the server in a background thread
* `daemon` — daemon flag for the thread

### Network Mode

* `internal` (default): use the host and SSL as given.
* `wired` or `wifi`: try to detect the local IP (contact 8.8.8.8 UDP) and disable SSL (set `sslc_local=False`) — useful for tethering/hotspots.
* `mesh`: read `mesh_peers.json` in `MODEL_DIR` (peer list) and enable mesh mode (the server remains passive, other peers exchange models manually via `post`).

### SSL Wrapping

If SSL is enabled, call `_auto_generate_cert(cert_dir)` to get the cert/key and then wrap the socket with `ssl.SSLContext`.

### Behavior

* Start a background loop: `start_domm_background_loop()`.
* If `threaded=True`: run the server in a daemon thread and return a thread object.
* If `threaded=False`: run the blocking `serve_forever()` (suitable for debugging in the terminal).

**Example:**

```python
from dearning.serving import run_server
run_server(host='0.0.0.0', port=8443, password='SECRET', sslc=True, mode='internal')
```

---

## Client Utilities (send & retrieve models)

### `_make_conn(host, port, sslc=True, timeout=30)`

Creates an `http.client.HTTPSConnection` (with a context that does not verify certificates) or an `HTTPConnection` if `use_ssl=False`.

> Note: `ssl._create_unverified_context()` is used to ensure connections to self-signed certs still succeed — meaning **no TLS verification**.

### `post(host, port, model_path, password, sslc=True, timeout=60, mode='internal')`

* Reads the `model_path` file, base64 encodes it, and POSTs it to `/import_model` with a JSON payload.
* For `wifi`/`wired` modes, if `host` is not provided, the function attempts to detect the default host and forces `sslc=False` and port 8080.
* Returns: a dict of results from the server or raises `FileNotFoundError` if the file does not exist.

**Example usage:**

```python
from dearning.serving import post
resp = post(host='192.168.1.10', port=8443, model_path='mymodel.dm', password='SECRET')
print(resp)
```

### `load(host, port, filename, password, sslc=True, timeout=60, mode='code', save_path=None)`

* Make a POST request to `/load_model` for `filename`.
* If the server returns `content_b64`, decode it and:

* `mode=='file'` → save to `save_path` (or to `MODEL_DIR`) and return the filename.

* `mode=='code'` → attempt to decode to UTF-8 and return the string "pretty" (removing `\r` and trailing spaces).
* If binary or decoding fails, return `bytes` of the data.

**Example:**

```python
from dearning.serving import load
code = load('192.168.1.10', 8443, 'mymodel.py', password='SECRET')
print(code[:400])
```

### Async wrappers

* `asynpost` and `asynload` wrap `post`/`load` using `asyncio.to_thread` so they can be called from coroutines without blocking.

---

## Integration Example: End-to-End

**Running a local server in the background:**

```python
from dearning.serving import run_server
run_server(password='mysecret', port=8443, sslc=False, threaded=True)
```

**Sending a model from the client:**

```python
from dearning.serving import post
resp = post(host='127.0.0.1', port=8443, model_path='mymodel.dm', password='mysecret', sslc=False)
print(resp)
```

**Retrieving a model from the server and saving it:**

```python
from dearning.serving import load
path = load('127.0.0.1', 8443, 'mymodel.dm', password='mysecret', sslc=False, mode='file')
print('saved to', path)
```

---

## Security & Hardening (Recommended)

This module is practical but needs some improvements before use on public networks:

1. **Authentication:**

* Don't send passwords via JSON body. Use the `Authorization: Bearer <token>` header or HTTP Basic Auth.
* Or use mutual TLS (client cert) for IoT/mesh.

2. **Transport Security:**

* Don't use `ssl._create_unverified_context()` on production clients — verify certificates.
* Use a valid CA certificate (Let's Encrypt) or a private CA.

3. **Rate limiting / brute force protection:**

* Limit password attempts per IP, add a lockout.

4. **File Validation & Sanitization:**

* Validate accepted file types (whitelist extensions).
* Limit allowed upload/download sizes (existing, but consider configurable limits).
* Store files in directories with restricted permissions.

5. **Auditing & Logging:**

* Enable centralized logging (file or syslog) with a minimum level of INFO.
* Record IP, user agent, and events (upload, download, error).

6. **Replace dummy cert behavior:**

* If OpenSSL is not available, do not automatically create a dummy cert in production—it is better to abort and issue owner instructions.

7. **Model Integrity:**

* Consider digitally signing models so clients can verify that the model comes from a trusted source.

8. **Sandboxing:**

* If the server runs or loads a `.py` model, do not import it directly without a sandbox—it carries the risk of executing arbitrary code.

---

## Unit tests & quality checks

* Test import_model with invalid base64
* Test import_model with path traversal attempts (../../) — ensured safe by basename
* Test load with cached & disk scenarios
* Test _auto_generate_cert behavior when openssl is available and not
* Test concurrency: multiple parallel uploads

---

## General troubleshooting

* **`PermissionError` when writing MODEL_DIR**: check permissions and the user running the process.
* **HTTPS self-signed errors**: client using `sslc=True` with default unverified context — browsers will error. Use `sslc=False` for LAN testing.
* **File not appearing in list**: ensure `filename` is correct and `path.exists()`.

---

## License and Ethics

Modules are infrastructure utilities — if used to share models, consider privacy, model licensing (whether they're allowed to be distributed), and compliance.

---
