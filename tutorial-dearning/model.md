`model` is a module for creating custom AI models.

---

## 1. Basic Architectural Concepts

### 1.1 Model Types

The models that can be built are **Sequential and graphical**

---
## 2. DOtensor
### 2.1 Definition
`DOtensor` stands for Dearning Of tensor. This function acts as an autograder for models, but currently it can only represent 1D and 2D vectors with the following capabilities:
* Arithmetic operations
* Computational graph construction
* Automatic backpropagation

DOtensor has several functions:
- backward
- zero_grad
- DTS
- trace
---
### 2.2 backward
The 'backward' function performs backpropagation on the computation graph.

Example code:
```python
from dearning import DOtensor
x = DOtensor([2.0, 3.0], requires_grad=True)
y = DOtensor([4.0, 5.0], requires_grad=True)
z = x * y + x
s = z.DTS()
s.backward()
```
Must be called on a scalar tensor (the final result).

---

### 2.3 zero_grad
The 'zero_grad' function resets the gradient.

Example code:
```python
x.zero_grad()
y.zero_grad()
```
---
### 2.4 DTS
The 'DTS' function sums all tensor elements into a single scalar value,
while preserving the autograd path for backpropagation.

Example code:
```python
from dearning import DOtensor
x = DOtensor([2.0, 3.0], requires_grad=True)
y = DOtensor([4.0, 5.0], requires_grad=True)
z = x * y + x
s = z.DTS()
```
---
### 2.5 Tracing
The 'trace' function is helpful for debugging, for example, to ensure proper graph alignment, the order of operations, and the like.
This function can be enabled or disabled as desired.

example code:
```python
with DOtensor.trace(enable=True) as log:
a = DOtensor([2.0], requires_grad=True)
b = DOtensor([3.0], requires_grad=True)
c = (a * b).DTS()
c.backward()
for step in log: print(step)
```
---
## 3. Sequence
### 3.1 Definition
`Sequence` is a collection of sequence modeling architectures implemented in pure Python.

Implemented models:
* `Sequence.RNN`
* `Sequence.GRU`
* `Sequence.LSTM`
* `Sequence.TCN`
* `Sequence.Transformer`

---

### 3.2 Data Representation
All tensors are represented as nested Python lists.

| Concept | Shape |
| ---------------------------- | ----------- |
| Single sequence | `[T][D]` |
| Set of sequences | `[B][T][D]` |
| Token sequence (Transformer) | `[T]` |
| Set of tokens | `[B][T]` |

The code automatically wraps single sequences into sets internally.

---

### 3.3. Sequence.RNN
```python
Sequence.RNN(input_dim, hidden_dim, output_dim=None, return_sequences=False)
```

| Parameters | Meaning |
| ------------------ | --------------------------------- |
| `input_dim` | Input feature size |
| `hidden_dim` | Hidden state size |
| `output_dim` | Output size (optional) |
| `return_sequences` | Return all time steps or only the last one |

---

#### 3.3.1 Definition
Classical Elman RNN with tanh activation.

---

#### 3.3.2 Forward Input/Output
Input:
* `[T][input_dim]` or `[B][T][input_dim]`

Output:
* If `return_sequences=False`: `[output_dim]` or `[B][output_dim]`
* If `True`: `[T][output_dim]` or `[B][T][output_dim]`

---

#### 3.3.3 Backward
```python
model.backward(grad_output)
```

* `grad_output` must match the shape of the forward output
* Returns the gradient with respect to input `X`

---

### 3.4. Sequence.GRU
```python
Sequence.GRU(input_dim, hidden_dim, output_dim=None, return_sequences=False)
Same semantics as `RNN`.
```
#### 3.4.1 Definition
Implements a **Gated Recurrent Unit** with update and reset gates.

Notes:
* Uses sigmoid and tanh activations
* Gradient flow is better than a regular RNN
* Backpropagation gates are completely manual

---

### 3.5. Sequence.LSTM
```python
Sequence.LSTM(input_dim, hidden_dim, output_dim=None, return_sequences=False)
```

#### 3.5.1 Definition
Long Short-Term Memory with input, forget, and output gates.

State variables:
* Hidden state `h_t`
* Cell state `c_t`

---

### 3.6. Sequence.TCN
```python
Sequence.TCN(input_dim, channels, kernel_size=3, dilations=None, output_dim=None, return_sequences=False)
```

| Parameters | Meaning |
| ----------- | ------------------------------- |
| `channels` | List of channel sizes per block |
| `dilations` | Dilations per block |

---

#### 3.6.1 Description
**Temporal Convolutional Network** with:
* Causal convolutions
* Dilations
* Residual connections
* ReLU activations

No recurrence. Fully parallel over time.

Notes:
* Uses causal convolution (no future leakage)
* Projects residuals when channels change
* Slower than NumPy-based CNNs

---

### 3.7. Sequence.Transformer
```python
Sequence.Transformer(d_model=32, num_heads=4, num_layers=2, d_ff=64, src_vocab_size=None, tgt_vocab_size=None, max_len=256, return_sequences=True)
```

#### 3.7.1 Definition
Transformer Encoder–Decoder implemented from scratch

Components:
* Token embedding
* Sinusoidal positional encoding
* Multi-head self-attention
* Cross-attention
* Feedforward layer
* Normalization layer

Input Format:
Source (`src`):
* Token ID `[T]` or `[B][T]`

Target (`tgt`):
* Token ID `[T]` or `[B][T]`

Output:
* `[T][vocab]` or just the last token

---

#### 3.7.2 Masking
* Decoder uses **causal mask**
* Prevents attending to future tokens

---
## 4. Dense Layer
### 4.1 Definition
`Dense` is a **fully connected linear layer**:

example code:
```python
from model import Dense
layer = Dense(input_dim=2, output_dim=3)
```

* Weights initialized with He-style scaling
* Bias initialized to zero
---
### 4.2 Forward Pass
example code:
```python
X = [[1.0, 2.0],
     [3.0, 4.0]]
out = layer.forward(X)
```

* Input must be 2D: `(batch_size × input_dim)`
* Output shape: `(batch_size × output_dim)`

---

### 4.3 Backward Pass
example code:
```python
grad_out = [[1.0, 1.0, 1.0], [1.0, 1.0, 1.0]]
grad_in = layer.backward(grad_out)
```

Computes:
* gradients for weights and bias,
* gradients propagated to previous layer.
---
### 4.4 Parameter Update
Uses standard gradient descent.
example code:
```python
layer.update(lr=0.01)
```
---
## 5. Activation Layer
### 5.1 Definition
Layer **non-linearity** into the network.
Supported activations:
* `relu`
* `sigmoid`
* `tanh`
* `softmax`
* `leaky_relu`
* `linear`

### 5.2 Forward pass
example code:
```python
from model import Activation
act = Activation("relu")
y = act.forward(x)
```
---
### 5.3 Backward Pass
example code:
```python
grad_in = act.backward(grad_out)
```
---

## 6. Dropout
### 6.1 Definition
`Dropout` applies **random neuron masking** during training to reduce overfitting.

---

### 6.2 Forward Pass
Each neuron is randomly set to zero with probability `rate`.
example code:
```python
from model import Dropout

drop = Dropout(rate=0.5)
out = drop.forward(x, training=True)
```

if training=False
```python
out = drop.forward(x, training=False)
```
then no masking is applied.

---

### 6.3 Backward Pass

```python
grad_in = drop.backward(grad_out)
```

Gradients are masked in the same pattern.

---

## 7. CustomAIModel
### 7.1 Definition
The `CustomAIModel` function is created and used to create AImodels. 

CustomAIModel has several functions:
- add (will be replaced by connect) 
- connect
- forward
- backward
- seed
- zero_grad
- step

available losses are only "mse", "cross_entropy", "softmax_cross_entropy"

---
### 7.2 connect
`connect` is the same function as add but can graph 

example code:
```python
from dearning import CustomAIModel, Dense
model = CustomAIModel() 
inp=[[1.0,0.6],[0.9,1.2]]
model.connect(Dense(2,1),inp)
``` 
---
### 7.3 forward
example code:
```python
y_pred = model.forward(X)
```
---
### 7.4 backward
example code:
```python
model.backward(grad)
```
---
### 7.5 seed
seed is a function to set the random seed.

example code:
```python
from dearning import CustomAIModel
model = CustomAIModel(loss="mse")
model.seed(42)
```
---
### 7.6 zero_grad
example code:
```python
model.zero_grad()
```
---
### 7.7 steps
The `step` function is used to update all model parameters using a certain learning rate.

example code:
```python
from dearning import CustomAIModel,Dense
model = CustomAIModel(loss="mse")
model.add(Dense(1, 1))
lr = 0.1
model.step(lr)
```
