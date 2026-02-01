`model` is a module for creating custom AI models.

---

## 1. Basic Architectural Concepts

### 1.1 Model Types

The models built are:
* **Sequential / Linear Chain**
* One-way data flow
* Does not support branching
---
## 2. DOtensor
### 2.1 Definition
`DOtensor` stands for Dearning Of tensor. This function acts as an autograder for models, but currently it can only represent 1D vectors with the following capabilities:
* Arithmetic operations
* Computational graph construction
* Automatic backpropagation
So this is not yet a general multidimensional tensor. (but will be continuously updated to allow for this)

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
## 3. Dense Layer
### Definition

`Dense` is a **fully connected linear layer**:

example code:
```python
from model import Dense
layer = Dense(input_dim=2, output_dim=3)
```

* Weights initialized with He-style scaling
* Bias initialized to zero
---
### 3.1 Forward Pass
example code:
```python
X = [[1.0, 2.0],
     [3.0, 4.0]]
out = layer.forward(X)
```

* Input must be 2D: `(batch_size × input_dim)`
* Output shape: `(batch_size × output_dim)`

---

### 3.2 Backward Pass
example code:
```python
grad_out = [[1.0, 1.0, 1.0],
            [1.0, 1.0, 1.0]]

grad_in = layer.backward(grad_out)
```

Computes:
* gradients for weights and bias,
* gradients propagated to previous layer.
---
### 3.3 Parameter Update
Uses standard gradient descent.
example code:
```python
layer.update(lr=0.01)
```
---
## 4. Activation Layer
### 4.1 Definition
Layer **non-linearity** into the network.
Supported activations:
* `relu`
* `sigmoid`
* `tanh`

example code:
```python
from model import Activation
act = Activation("relu")
y = act.forward(x)
```
---
### 4.2 Backward Pass
example code:
```python
grad_in = act.backward(grad_out)
```
---

## 5. Dropout
### 5.1 Definition
`Dropout` applies **random neuron masking** during training to reduce overfitting.

---

### 5.2 Forward Pass
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

### 5.3 Backward Pass

```python
grad_in = drop.backward(grad_out)
```

Gradients are masked in the same pattern.

---

## 6. CustomAIModel
### 6.1 Definition
The `CustomAIModel` function is created and used to create AI models, but currently, only sequential or linear chain models can be created.

CustomAIModel has several functions:
- add
- forward
- backward
- seed
- zero_grad
- step
---
### 6.2 add
The `add` function is used to create layers.

Example code:
```python
from dearning import CustomAIModel
model = CustomAIModel()
model.add(Dense(1, 1))
```
---
### 6.3 forward
example code:
```python
y_pred = model.forward(X)
```
---
### 6.4 backward
example code:
```python
model.backward(grad)
```
---
### 6.5 seed
seed is a function to set the random seed.

example code:
```python
from dearning import CustomAIModel
model = CustomAIModel(loss="mse")
model.seed(42)
```
---
### 6.6 zero_grad
example code:
```python
model.zero_grad()
```
---
### 6.7 steps
The `step` function is used to update all model parameters using a certain learning rate.

example code:
```python
from dearning import CustomAIModel,Dense
model = CustomAIModel(loss="mse")
model.add(Dense(1, 1))
lr = 0.1
model.step(lr)
```
