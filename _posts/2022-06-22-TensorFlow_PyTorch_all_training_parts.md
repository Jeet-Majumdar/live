---
layout: post
title: TensorFlow vs. PyTorch - All important parts of Machine Learning
categories: [Tech]
---

### Datasets

**TensorFLow**

```python
tensorflow.keras.datasets
```

**PyTorch**

```python
from torch.utils.data import Dataset
from torchvision import datasets
```

---------

### Transforms

**TensorFlow**

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator
from tensorflow.keras.preprocessing.image import img_to_array, load_img

train_datagen = ImageDataGenerator(rescale=1.0/255,
                                     rotation_range=40,
                                     width_shift_range=0.2,
                                     height_shift_range=0.2,
                                     shear_range=0.2,
                                     zoom_range=0.2,
                                     horizontal_flip=True,
                                     fill_mode='nearest')

  # Pass in the appropriate arguments to the flow_from_directory method
  train_generator = train_datagen.flow_from_directory(directory=TRAINING_DIR,
                                                      batch_size=20,
                                                      class_mode='binary',
                                                      target_size=(150, 150))
```



**PyTorch**

```pyth
from torchvision.transforms import ToTensor, Lambda

training_data = datasets.FashionMNIST(
    root="data",
    train=True,
    download=True,
    transform=ToTensor()
)

train_dataloader = DataLoader(training_data, batch_size=64, shuffle=True)

train_features, train_labels = next(iter(train_dataloader))
```

------------

### Model Building

**TensorFlow**

```pytho
model = tf.keras.models.Sequential([
      tf.keras.layers.Conv2D(32, (3,3), activation='relu', input_shape=(150, 150, 3)),
      tf.keras.layers.MaxPooling2D(2, 2),
      tf.keras.layers.Conv2D(64, (3,3), activation='relu'),
      tf.keras.layers.MaxPooling2D(2,2),
      tf.keras.layers.Flatten(),
      tf.keras.layers.Dense(512, activation='relu'),
      tf.keras.layers.Dense(1, activation='sigmoid')
  ])

  
model.compile(optimizer=tf.keras.optimizers.RMSprop(learning_rate=0.001),
                loss=tf.keras.losses.BinaryCrossentropy(),
                metrics=['accuracy'])
```



**PyTorch**

```python
class NeuralNetwork(nn.Module):
    def __init__(self):
        super(NeuralNetwork, self).__init__()
        self.flatten = nn.Flatten()
        self.linear_relu_stack = nn.Sequential(
            nn.Linear(28*28, 512),
            nn.ReLU(),
            nn.Linear(512, 512),
            nn.ReLU(),
            nn.Linear(512, 10),
        )

    def forward(self, x):
        x = self.flatten(x)
        logits = self.linear_relu_stack(x)
        return logits

device = "cuda" if torch.cuda.is_available() else "cpu"
print(f"Using {device} device")

model = NeuralNetwork().to(device)
print(model)
```



```python
model = nn.Sequential(
          nn.Conv2d(1,20,5),
          nn.ReLU(),
          nn.Conv2d(20,64,5),
          nn.ReLU()
        )
input_image = torch.rand(3,28,28)
logits = seq_modules(input_image)
```

--------------

### Gradient and Automatic differentiation

**TensorFlow**

```python
w = tf.Variable(tf.random.normal((3, 2)), name='w')
b = tf.Variable(tf.zeros(2, dtype=tf.float32), name='b')
x = [[1., 2., 3.]]

with tf.GradientTape(persistent=True) as tape:
  y = x @ w + b
  loss = tf.reduce_mean(y**2)
    
[dl_dw, dl_db] = tape.gradient(loss, [w, b])
```

```python
layer = tf.keras.layers.Dense(2, activation='relu')
x = tf.constant([[1., 2., 3.]])

with tf.GradientTape() as tape:
  # Forward pass
  y = layer(x)
  loss = tf.reduce_mean(y**2)

# Calculate gradients with respect to every trainable variable
grad = tape.gradient(loss, layer.trainable_variables)
```

The following fails to calculate a gradient because the [`tf.Tensor`](https://www.tensorflow.org/api_docs/python/tf/Tensor) is not "watched" by default, and the [`tf.Variable`](https://www.tensorflow.org/api_docs/python/tf/Variable) is not trainable:

```python
# A trainable variable
x0 = tf.Variable(3.0, name='x0')
# Not trainable
x1 = tf.Variable(3.0, name='x1', trainable=False)
# Not a Variable: A variable + tensor returns a tensor.
x2 = tf.Variable(2.0, name='x2') + 1.0
# Not a variable
x3 = tf.constant(3.0, name='x3')

with tf.GradientTape() as tape:
  y = (x0**2) + (x1**2) + (x2**2)

grad = tape.gradient(y, [x0, x1, x2, x3])

for g in grad:
  print(g)
```

> ```
> tf.Tensor(6.0, shape=(), dtype=float32)
> None
> None
> None
> ```

To record gradients with respect to a [`tf.Tensor`](https://www.tensorflow.org/api_docs/python/tf/Tensor), you need to call [`GradientTape.watch(x)`](https://www.tensorflow.org/api_docs/python/tf/GradientTape#watch):

```python
x = tf.constant(3.0)
with tf.GradientTape() as tape:
  tape.watch(x)
  y = x**2

# dy = 2x * dx
dy_dx = tape.gradient(y, x)
print(dy_dx.numpy())
```

> 6.0

Conversely, to disable the default behavior of watching all `tf.Variables`, set `watch_accessed_variables=False` when creating the gradient tape.

```python
x0 = tf.Variable(0.0)
x1 = tf.Variable(10.0)

with tf.GradientTape(watch_accessed_variables=False) as tape:
  tape.watch(x1)
  y0 = tf.math.sin(x0)
  y1 = tf.nn.softplus(x1)
  y = y0 + y1
  ys = tf.reduce_sum(y)

# dys/dx1 = exp(x1) / (1 + exp(x1)) = sigmoid(x1)
grad = tape.gradient(ys, {'x0': x0, 'x1': x1})

print('dy/dx0:', grad['x0'])
print('dy/dx1:', grad['x1'].numpy())
```

> ```
> dy/dx0: None
> dy/dx1: 0.9999546
> ```

To compute multiple gradients over the same computation, create a gradient tape with `persistent=True`. This allows multiple calls to the `gradient` method as resources.

```python
x = tf.constant([1, 3.0])
with tf.GradientTape(persistent=True) as tape:
  tape.watch(x)
  y = x * x
  z = y * y

print(tape.gradient(z, x).numpy())  # [4.0, 108.0] (4 * x**3 at x = [1.0, 3.0])
print(tape.gradient(y, x).numpy())  # [2.0, 6.0] (2 * x at x = [1.0, 3.0])
```

> ```
> [  4. 108.]
> [2. 6.]
> ```



**PyTorch**

```python
import torch

x = torch.ones(5)  # input tensor
y = torch.zeros(3)  # expected output

w = torch.randn(5, 3, requires_grad=True)
b = torch.randn(3, requires_grad=True)
z = torch.matmul(x, w)+b

loss = torch.nn.functional.binary_cross_entropy_with_logits(z, y)

loss.backward()

print(w.grad)
print(b.grad)
```

- We can only perform gradient calculations using `backward` once on a given graph, for performance reasons. If we need to do several `backward` calls on the same graph, we need to pass `retain_graph=True` to the `backward` call.

We can stop tracking computations by surrounding our computation code with `torch.no_grad()` block:

```python
z = torch.matmul(x, w)+b
print(z.requires_grad)

with torch.no_grad():
    z = torch.matmul(x, w)+b
print(z.requires_grad)
```

> True
> False

Reasons for disabling gradient calculation: to mark some parameters in the neural network as **frozen parameters**.

--------------------

### Jacobian and Jacobian Products

Differentiation of a vector with respect to another vector: 

$ \vec{x}=\langle x_1,\dots,x_n\rangle$ and $\vec{y}=\langle y_1,\dots,y_m\rangle$, a gradient of $\vec{y}$ with respect to $\vec{x}$ is given by **Jacobian matrix**:
$$
J=\left(\begin{array}{ccc} \frac{\partial y_{1}}{\partial x_{1}} & \cdots & \frac{\partial y_{1}}{\partial x_{n}}\\ \vdots & \ddots & \vdots\\ \frac{\partial y_{m}}{\partial x_{1}} & \cdots & \frac{\partial y_{m}}{\partial x_{n}} \end{array}\right)
$$
**TensorFlow**

Scalar source:

```python
x = tf.linspace(-10.0, 10.0, 200+1)
delta = tf.Variable(0.0)

with tf.GradientTape() as tape:
  y = tf.nn.sigmoid(x+delta)

dy_dx = tape.jacobian(y, delta)		# y is the target, delta is the source
```

Jacobian with respect to a scalar the result has the shape of the **target**, and gives the gradient of the each element with respect to the source. 

Tensor Source:

```python
x = tf.random.normal([7, 5])
layer = tf.keras.layers.Dense(10, activation=tf.nn.relu)

with tf.GradientTape(persistent=True) as tape:
  y = layer(x)

print(y.shape)
print(layer.kernel.shape)
# The shape of the Jacobian of the output with respect to the kernel is those two shapes concatenated together:
j = tape.jacobian(y, layer.kernel)
print(j.shape)
```

> TensorShape([7, 10])
> TensorShape([5, 10]) 
> TensorShape([7, 10, 5, 10])

If you sum over the **target's** dimensions, you're left with the gradient of the sum that would have been calculated by [`tf.GradientTape.gradient`](https://www.tensorflow.org/api_docs/python/tf/GradientTape#gradient):

```python
j_sum = tf.reduce_sum(j, axis=[0, 1]) # j_sum will be same as tape.gradient(y, layer.kernel)
```



**PyTorch**

Instead of computing the Jacobian matrix itself, PyTorch allows you to compute **Jacobian Product** $v^T\cdot J$ for a given input vector $v=(v_1 \dots v_m)$. This is achieved by calling `backward` with *v* as an argument. 

```python
inp = torch.eye(5, requires_grad=True)
out = (inp+1).pow(2)

out.backward(torch.ones_like(inp), retain_graph=True)
print(f"First call\n{inp.grad}")

out.backward(torch.ones_like(inp), retain_graph=True)
print(f"\nSecond call\n{inp.grad}")

inp.grad.zero_()

out.backward(torch.ones_like(inp), retain_graph=True)
print(f"\nCall after zeroing gradients\n{inp.grad}")
```

when we call `backward` for the second time with the same argument, the value of the gradient is different. This happens because when doing `backward` propagation, PyTorch **accumulates the gradients**, 

If we want to compute the proper gradients, you need to zero out the `grad` property before.

------------

### Optimizing Loop

**TensorFlow**

```python
epochs = 50
loss_fn = keras.losses.SparseCategoricalCrossentropy(from_logits=True)
optimizer = keras.optimizers.SGD(learning_rate=1e-3) 

# Create a callback that saves the model's weights every 5 epochs
cp_callback = tf.keras.callbacks.ModelCheckpoint(
    filepath=checkpoint_path, 
    verbose=1, 
    save_weights_only=True,
    save_freq=5*batch_size)

model.fit(train_images, 
          train_labels,
          epochs=epochs, 
          batch_size=32, 
          callbacks=[cp_callback],
          validation_data=(test_images, test_labels),
          verbose=0)
```

Custom Loop:

```python
epochs = 2
loss_fn = keras.losses.SparseCategoricalCrossentropy(from_logits=True)
optimizer = keras.optimizers.SGD(learning_rate=1e-3) 

for epoch in range(epochs):
    for batch, (x_batch_train, y_batch_train) in enumerate(train_dataset):
  
        with tf.GradientTape() as tape:
            logits = model(x_batch_train, training=True)
            loss_value = loss_fn(y_batch_train, logits)
  
        grads = tape.gradient(loss_value, model.trainable_weights # NEW
  
        optimizer.apply_gradients(zip(grads, model.trainable_weights)) # NEW
  
        # Log every 200 batches.
        if batch % 200 == 0:
            print(
                "Training loss (for one batch) at batch %d: %.4f"
                % (batch, float(loss_value))
            )
            print("Seen so far: %s samples" % ((batch + 1) * batch_size))
```



**PyTorch**

Ingredients:

- Loss Function: `loss_fn = nn.CrossEntropyLoss()`
- Optimizer : `optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)`

Each epoch consists of two main parts:

- **The Train Loop** - iterate over the training dataset and try to converge to optimal parameters.
- **The Validation/Test Loop** - iterate over the test dataset to check if model performance is improving.



```python
def train_loop(dataloader, model, loss_fn, optimizer):
    size = len(dataloader.dataset)
    for batch, (X, y) in enumerate(dataloader):
        # Compute prediction and loss
        pred = model(X)
        loss = loss_fn(pred, y)

        # Backpropagation
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        if batch % 100 == 0:
            loss, current = loss.item(), batch * len(X)
            print(f"loss: {loss:>7f}  [{current:>5d}/{size:>5d}]")


def test_loop(dataloader, model, loss_fn):
    size = len(dataloader.dataset)
    num_batches = len(dataloader)
    test_loss, correct = 0, 0

    with torch.no_grad():
        for X, y in dataloader:
            pred = model(X)
            test_loss += loss_fn(pred, y).item()
            correct += (pred.argmax(1) == y).type(torch.float).sum().item()

    test_loss /= num_batches
    correct /= size
    print(f"Test Error: \n Accuracy: {(100*correct):>0.1f}%, Avg loss: {test_loss:>8f} \n")


loss_fn = nn.CrossEntropyLoss()
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)

epochs = 10
for t in range(epochs):
    print(f"Epoch {t+1}\n-------------------------------")
    train_loop(train_dataloader, model, loss_fn, optimizer)
    test_loop(test_dataloader, model, loss_fn)
print("Done!")
```



### Saving Model 

**TensorFlow**

Saving and loading Model Weight:

```python
# Save the weights
model.save_weights('./checkpoints/my_checkpoint')
# Restore the weights
model.load_weights('./checkpoints/my_checkpoint')
```

Saving and Loading Models with Shapes (Structure of the Model):

```python
# Save the entire model as a SavedModel.
model.save('saved_model/my_model')
new_model = tf.keras.models.load_model('saved_model/my_model')
```



**PyTorch**

Saving and Loading Model Weights:

```python
model = models.vgg16(pretrained=True)
torch.save(model.state_dict(), 'model_weights.pth')
```

To load model weights, you need to create an instance of the same model first, and then load the parameters using `load_state_dict()` method.

```python
model = models.vgg16() # we do not specify pretrained=True, i.e. do not load default weights
model.load_state_dict(torch.load('model_weights.pth'))
model.eval()
```

Saving and Loading Models with Shapes (Structure of the Model):

```python
torch.save(model, 'model.pth')
model = torch.load('model.pth')
```



- References:
  1. https://www.tensorflow.org/api_docs/python/tf/all_symbols
  2. https://pytorch.org/docs/1.11/