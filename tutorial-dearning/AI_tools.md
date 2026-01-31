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

## 2. RLTools – Reinforcement Learning Minimal Framework
Still under development
### 2.1 Definition
RL framework **concise and explicit**, suitable for:

---

### 2.2 Environment
#### `Env` (Abstract)
```python
reset()
step(action)
actions()
state()
```

#### `SimpleEnv`
1D environment:
* State: integer
* Action: `-1`, `+1`
* Reward:
* `+1` if goal is reached
* `-0.01` otherwise

example code:
```python
env = RLTools.SimpleEnv(start=0, goal=10)
```

---

### 2.3 Policies
#### `EpsilonGreedy`
```python
policy = RLTools.EpsilonGreedy(eps=0.1)
```

#### `Softmax`
```python
policy = RLTools.Softmax(temp=1.0)
```

---

### 2.4 Agents
```python
agent = RLTools.Agent(alpha=0.1, gamma=0.9)
```

* `q[(state, action)]`
* Support: 
* Q-learning 
* SARSA

---

### 2.5 Trainers
```python
trainer = RLTools.Trainer(env, agent, episodes=1000, max_steps=100, mode="q" # or "sarsa")
```

Exercise:
```python
log = trainer.train()
```

---

### 2.6 Evaluators
```python
eval = RLTools.Evaluator(env, agent)
eval.run(episodes=100)
```

Output:
```python
{"mean": float, "max": float, "min": float}
```

---

### 2.7 Runners & Hooks
Event-driven training:
```python
runner = RLTools.Runner(trainer)
runner.engine.on("start", lambda: print("start"))
runner.engine.on("end", lambda log: print("done"))
runner.run()
```

---

## 3. image
### 3.1 `image.load`
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

### 3.2 `image.resize`
```python
image.resize(pix, w, h, target=32, is_rgb=True)
```

---

### 3.3 `image.save`
```python
image.save("out.pgm", pix, size=64, ch=1)
```

---

## 4. videos
### 4.1 understanding
to make the model can support video(but GIF)

---

### 4.2 `video.gif`
```python
frames = video.gif("anim.gif", target_size=(64,64), as_rgba=True, max_frames=10)
```

#### Output
List of:
```python
(pixels, (w,h), channels, duration_ms)
```

---

## 5. Qkanalyze
### 5.1 `top_kprobs`
just take the K largest probability from the lists
```python
Qkanalyze.top_kprobs(preds, k=3)
```
---

### 5.2 `summarize_array`
Summary of 1D or 2D list values.
```python
Qkanalyze.summarize_array(arr)
```

---
