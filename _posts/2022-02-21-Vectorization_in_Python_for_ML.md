---
layout: post
title: Vectorization in Python for ML
categories: [Tech]
---

**Vectorization in Python for ML**

Using : SIMD - Single Instruction Multiple Data

Lets say your data has the following structure:

**x1**, **x2**, **x3**, ... **xn** are the n data points with some dimension (k). Let these be organized as column vectors so that **X** = [**x1**  **x2**  **x3** ... **xn** ] is a (k,n) dimension vector. 

Then to perform the unit operation of a data of the form $z = wx + b$, the python equivalent will be:

```python
Z = np.dot(W.T, X) + b
```

Where **W** is the weight matrix of the same dimension as **X**, and *W.T* gives the transpose of **W**. 

*b* is the constant value which python will  broadcast into array in its vectorization loop.

Thus *Z* will be a row vector.

**Logistic Regression in Vectorized notation**

```python
from scipy.special import expit
import numpy as np
Z = np.dot(W.T, X) + b
A = expit(Z)
dZ = A.Y
dW = (1/m)*np.dot(X, dZ.T)
db = (1/m) * np.sum(dZ)

W = W - lr * dW
b = b - lr * db
```



> Note: For safety reason, when creating random vector, use explicit dimensioning. 
>
> Example: `a = np.random.randn(5, 1)` as opposed to `a = np.random.randn(5)`

To make sure that a vector is of certain shape use `assert(a.shape == (5, 1))`

`z = z * b` does element wise multiplication

Vectorization Example:

```python
data = np.array([[1,1,1],[2,2,2],[3,3,3]])

vector = np.array([1,2,3])

data / vector.reshape(-1,1)  # np.array([[1,1,1],[1,1,1],[1,1,1]])
```





**Some common vectorized and normal implementation**

*Normal*

```python
import time

x1 = [9, 2, 5, 0, 0, 7, 5, 0, 0, 0, 9, 2, 5, 0, 0]
x2 = [9, 2, 2, 9, 0, 9, 2, 5, 0, 0, 9, 2, 5, 0, 0]

### CLASSIC DOT PRODUCT OF VECTORS IMPLEMENTATION ###
tic = time.process_time()
dot = 0

for i in range(len(x1)):
    dot += x1[i] * x2[i]
toc = time.process_time()
print ("dot = " + str(dot) + "\n ----- Computation time = " + str(1000 * (toc - tic)) + "ms")

### CLASSIC OUTER PRODUCT IMPLEMENTATION ###
tic = time.process_time()
outer = np.zeros((len(x1), len(x2))) # we create a len(x1)*len(x2) matrix with only zeros

for i in range(len(x1)):
    for j in range(len(x2)):
        outer[i,j] = x1[i] * x2[j]
toc = time.process_time()
print ("outer = " + str(outer) + "\n ----- Computation time = " + str(1000 * (toc - tic)) + "ms")

### CLASSIC ELEMENTWISE IMPLEMENTATION ###
tic = time.process_time()
mul = np.zeros(len(x1))

for i in range(len(x1)):
    mul[i] = x1[i] * x2[i]
toc = time.process_time()
print ("elementwise multiplication = " + str(mul) + "\n ----- Computation time = " + str(1000 * (toc - tic)) + "ms")

### CLASSIC GENERAL DOT PRODUCT IMPLEMENTATION ###
W = np.random.rand(3,len(x1)) # Random 3*len(x1) numpy array
tic = time.process_time()
gdot = np.zeros(W.shape[0])

for i in range(W.shape[0]):
    for j in range(len(x1)):
        gdot[i] += W[i,j] * x1[j]
toc = time.process_time()
print ("gdot = " + str(gdot) + "\n ----- Computation time = " + str(1000 * (toc - tic)) + "ms")
```

*Vectorized*

```python
x1 = [9, 2, 5, 0, 0, 7, 5, 0, 0, 0, 9, 2, 5, 0, 0]
x2 = [9, 2, 2, 9, 0, 9, 2, 5, 0, 0, 9, 2, 5, 0, 0]

### VECTORIZED DOT PRODUCT OF VECTORS ###
tic = time.process_time()
dot = np.dot(x1,x2)
toc = time.process_time()
print ("dot = " + str(dot) + "\n ----- Computation time = " + str(1000 * (toc - tic)) + "ms")

### VECTORIZED OUTER PRODUCT ###
tic = time.process_time()
outer = np.outer(x1,x2)
toc = time.process_time()
print ("outer = " + str(outer) + "\n ----- Computation time = " + str(1000 * (toc - tic)) + "ms")

### VECTORIZED ELEMENTWISE MULTIPLICATION ###
tic = time.process_time()
mul = np.multiply(x1,x2)
toc = time.process_time()
print ("elementwise multiplication = " + str(mul) + "\n ----- Computation time = " + str(1000*(toc - tic)) + "ms")

### VECTORIZED GENERAL DOT PRODUCT ###
tic = time.process_time()
dot = np.dot(W,x1)
toc = time.process_time()
print ("gdot = " + str(dot) + "\n ----- Computation time = " + str(1000 * (toc - tic)) + "ms")
```



Transforming an image to a desired size:

```python
from PIL import Image
my_image = "my_image.jpg" 
image = np.array(Image.open(my_image).resize((num_px, num_px)))
```



Using Matplotlib to show image:

```python
import matplotlib.pyplot as plt 
plt.imshow(image)
```



Basic training loop:

```python
Model:
    Initialize variables
    Optimize
    Predict
Optimize:
    Loop:
        Propagate {returns: grads, cost}
        Update variables with grads
        Store cost with iteration
Propagate:
    Fordward prop:
        Calculate gradient and store
    Calculate cost
    Backward prop:
        Input gradient from forward layer
        Update parameter of this layer
        Pass updated gradient backward        
```



 We use (keepdims = True) to make sure that A.shape is (4,1) and not (4, ).

Example:

```python
A = np.random.randn(4,3)
B = np.sum(A, axis = 1, keepdims = True) 
print(B.shape) # (4, 1)
```

