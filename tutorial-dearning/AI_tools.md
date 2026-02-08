`AI_tools` is a module for enriching model features.


## 1. DLP
```python
from dearning import DLP
dlp = DLP(lang="en")
```
`lang` currently **does not affect logic**, only metadata.

### 1.1 definition
`DLP` is **lightweight, rule-based NLP**, not a statistical model or transformer. Suitable for:
* Fast sentiment analysis
* Basic information extraction

---

### 1.2 `analyze_sentiment`
```python
dlp.analyze_sentiment(text)
```

* Manual tokenization (alphabet only)
* Count:
* `positive_words`
* `negative_words`

example code:
```python
dlp.analyze_sentiment("Amazing and wonderful results")
```

---

### 1.3 `extract_nouns`
```python
dlp.extract_nouns(text)
```
#### Rules
* Capitalize initial words
* Length > 3 characters

example code:
```python
dlp.extract_nouns("Python is Loved by Developers Worldwide")
# ["Python", "Loved", "Developers", "Worldwide"]
```

---

### 1.4 `pos_tagging`
```python
dlp.pos_tagging(text)
```

#### Simple POS rules
| Condition | Tag |
| ---------------- | ----- |
| Capitalized | `NNP` |
| Ends with `"ing"` | `VBG` |
| Default | `NN` |

---

### 1.5 `summarize`
```python
dlp.summarize(text, max_sentences=2)
```
* Split by `. ! ?`
* Take the initial sentence

---

### 1.6 `process`
```python
dlp.process(text)
```

---

### 1.7 Tokenizer
#### 1.7.1 Definition
`Tokenizer` is a simple character-level tokenizer that converts text into a sequence of integer IDs and can return it back to text.

Key characteristics:
* Tokenization is character-based, not word-based
* Vocabulary is built dynamically from training data
* Uses explicit special tokens

When `fit()` is called, the tokenizer builds the vocabulary:
```text
["<pad>", "<unk>", "<bos>", "<eos>"] + sorted(unique_characters)
```

Special tokens meaning:
| Token | Function |
| ------- | -------------------------------- |
| `<pad>` | Padding (not used automatically) |
| `<unk>` | Unknown character |
| `<bos>` | Beginning of sequence |
| `<eos>` | End of sequence |

Vocabulary is **deterministic** because characters are sorted.

The **required** usage sequence is:
```text
Tokenizer()
↓
fit(texts)
↓
encode(text)
↓
decode(ids)
```
If `encode()` is called before `fit()` → **RuntimeError**.

---

### 1.7.2 `Tokenizer()`
Creates an empty tokenizer instance.

```python
char2id = {}
id2char = {}
fitted = False
```

Example code:
```python
tok = DLP.Tokenizer()
```

---

### 1.7.3 `fit(texts)`
| Name | Type | Description |
| ------- | ----------- | ---------------------- |
| `texts` | `list[str]` | Collection of training texts |

Building a vocabulary from a collection of texts.

Behavior:
* Captures **all unique characters**
* Adds special tokens
* Fills `char2id` and `id2char`
* Marks tokenizer as `fitted`

Example code:
```python
tok = DLP.Tokenizer()
tok.fit(["Quantum", "Computing", "is", "the", "future"])
```

---

### 1.7.4 `encode(text, add_special=True)`
| Name | Type | Default | Description |
| ------------- | ------ | ------- | -------------------------- |
| `text` | `str` | – | Text input |
| `add_special` | `bool` | `True` | Add `<bos>` and `<eos>` |

Converts a string to a list of integer IDs.

Important behavior:
* Unknown character → `<unk>`
* If `add_special=True`:
```
[<bos>] + tokens + [<eos>]
```

Example code:
```python
tok.encode("Quantum")
# [2, 10, 21, 0, ..., 3] (example)
```

Without special token:
```python
tok.encode("Quantum", add_special=False)
```

---

### 1.7.5 `decode(ids, skip_special=True)`
| Name | Type | Default | Description |
| -------------- | ---------------------------------- | ------- | ----------------------------- |
| `ids` | `list[int]` or `list[list[int]]` | – | Sequence ID |
| `skip_special` | `bool` | `True` | Ignore `<bos>`, `<eos>`, etc. |

Converts IDs back to text.

Important behavior:
* Supports **batch decoding**
* Special tokens are automatically skipped (default)
* Unknown IDs → ignored (`""`)

Example code:
```python
ids = tok.encode("Quantum Computing")
tok.decode(ids)
# "Quantum Computing"
```

---
## 2. image
### 2.1 `image.load`
```python
pix, (w, h), ch = image.load(path, target_size=(64,64), grayscale=True)
```

#### Supported
* PNG
* PGM / PPM (PNM)
* Resize (nearest neighbors)
* Grayscale conversion

#### Output
* `pix`: list of floats `[0.0–1.0]`
* `ch`: channels (1, 3, 4)

---

### 2.2 `image.resize`
```python
image.resize(pix, w, h, target=32, is_rgb=True)
```

---

### 2.3 `image.save`
```python
image.save("out.pgm", pix, size=64, ch=1)
```

---

## 3. videos
### 3.1 understanding
to make the model can support video(but GIF)

---

### 3.2 `video.gif`
```python
frames = video.gif("anim.gif", target_size=(64,64), as_rgba=True, max_frames=10)
```

#### Output
List of:
```python
(pixels, (w,h), channels, duration_ms)
```

---

## 4. Qkanalyze
### 4.1 `top_kprobs`
just take the K largest probability from the lists
```python
Qkanalyze.top_kprobs(preds, k=3)
```
---

### 4.2 `summarize_array`
Summary of 1D or 2D list values.
```python
Qkanalyze.summarize_array(arr)
```

---
