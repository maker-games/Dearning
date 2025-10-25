The model is first module provides a pure Python-based artificial neural network (AI) modeling system.
The structure is modular and lightweight, but still supports modern features such as:
* `Dense` layers (fully connected)
* Activation functions (`ReLU`, `Sigmoid`, `Tanh`)
* `Dropout` layers
* Manual backpropagation
* Saving and loading models (`save_model` and `load_model`)
* Additional neuron types (Memory, Attention, NALU, Mixture of Experts, Graph, etc.)

## 1. Basic Model Structure
1.1. CustomAIModel

The main class for building and training AI models.

Features:
* Adding layers (Dense, Activation, Dropout)
* Supports forward pass, backpropagation, and weight updates
* Saving and loading models
* Adding custom neurons (Memory, Attention, NALU, Graph, etc.)

Example:
```python
from dearning import CustomAIModel, Dense, Activation
# Creating a new model
model = CustomAIModel(loss="mse")

# Adding Dense and Activation layers
model.add(Dense(4, 8)) # Layer input=4, output=8
model.add(Activation("relu")) # ReLU activation function
model.add(Dense(8, 1)) # Layer Output
```

1.2. Dense(input_dim, output_dim)

Fully Connected neural network layer.
Function: Calculates the linear result of the input and weights.
y = xW + b

Example:
```python
model.add(Dense(4, 16))
```

1.3. Activation(kind)

Adds a non-linear activation function.
Supported types:
* "relu"
* "sigmoid"
* "tanh"

Example:
```python
model.add(Activation("relu"))
```

1.4. Dropout(rate)

Used to prevent overfitting by deactivating some neurons.

Example:
```python
model.add(Dropout(0.3)) # 30% of neurons will be deactivated during training
```

1.5. Proses Forward dan Backward

Setiap layer mendukung:
* forward(x) → proses maju menghitung output
* backward(grad) → menghtung gradien untuk pembaruan bobot

Semua ini berjalan otomatis saat:
```python
model.train(X, y, epochs=20, learning_rate=0.01)
```

## 2. Model Training
```python
# Example Data
X = [[0.1, 0.2, 0.3, 0.4],
[0.4, 0.3, 0.2, 0.1]]
y = [[0.5], [0.2]]
# Train the model for 10 epochs
model.train(X, y, epochs=10, learning_rate=0.01)
```
```output
Output:
Epoch 1/10, Loss: 0.1342
Epoch 2/10, Loss: 0.1208
```

## 3. Saving and Loading the Model
```python
model.save_model("mymodel")
loaded = CustomAIModel.load_model("mymodel")
```
A saved model produces two files:
* `mymodel_config.json`
* `mymodel_weights.json`

## 4. Adding Advanced Neurons

You can add additional neurons to the model
```python
model.addneuron(kind="memory", size=64)
model.addneuron(kind="attention")
model.addneuron(kind="nalu")
model.addneuron(kind="graph", nodes=[1,2,3], edges=[(1,2),(2,3)])
model.addneuron(kind="spiking")
```

Each neuron type has a specific function:
* 🧠 **Memory** → internal model storage
* 🎯 **Attention** → selection of important information
* 🧮 **NALU** → Logical arithmetic calculations
* 🔗 **Graph** → Inter-node relationships (Graph Network)
* ⚡ **Spiking** → Nerve impulse simulation

## 5. DOtensor

The DOtensor class is used for basic tensor.

Features:
* +, * operations between tensors
* Gradient tracking support
* Trace mode for debugging

Example:
```python
from dearning import DOtensor
DOtensor.enable_trace()
a = DOtensor([1, 2])
b = DOtensor([3, 4])
c = a * b + a
print(DOtensor.get_trace_log())
```
```output
Output:
[('mul', [1, 2], [3, 4]), ('add', [1, 2], [3, 4])]
```





