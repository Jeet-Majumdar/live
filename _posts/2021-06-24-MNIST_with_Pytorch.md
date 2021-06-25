---
layout: post
title: MNIST with PyTorch
categories: [Tech]
---

Let's first understand the *"ground truth"*. How our actual data looks like which we intent to feed into the network. 

In this work, we will be using the MNIST dataset in .csv format. 

The dataset is provided in Kaggle, and you may download it from [here](https://www.kaggle.com/oddrationale/mnist-in-csv) to follow along. There are two important .csv files: *mnist_train.csv*, and *mnist_test.csv*.  Download the two in your working directory which I will take as my root directory. 

Every row of the csv files contain flattented out pixel of an image matrix of a single channel. The images are of handwritten digits from 0 to 9. The first field of every row in the csv files represent this target value. Thus, total number of columns = total number of image pixels in an image + 1.  For MNIST, this is 785 = 28*28 +1

Python implementation of the above lines:

```python
import pandas as pd 
nRowsRead = None 
train_dataset = pd.read_csv('/mnist-original/mnist_train.csv', delimiter=',', nrows = nRowsRead)
train_dataset.dataframeName = 'mnist_train.csv'
nRow, nCol = train_dataset.shape
print(f'There are {nRow} rows and {nCol} columns in train data')

nRowsRead = None 
test_dataset = pd.read_csv('/mnist-original/mnist_test.csv', delimiter=',', nrows = nRowsRead)
test_dataset.dataframeName = 'mnist_test.csv'
nRow, nCol = test_dataset.shape
print(f'There are {nRow} rows and {nCol} columns in test data')
```

> ```
> There are 59999 rows and 785 columns in train data
> There are 9999 rows and 785 columns in test data
> ```

```python
print("Training Dataset Lookup:")
train_dataset.head()
print("Test Dataset Lookup:")
test_dataset.head()
```

At this stage, if you did everything correctly, all the images are loaded as a pandas dataframe.

**Now lets implement the neural network in PyTorch!**



Import the important pytorch libraries:

```python
import torch
import torch.nn as nn		# For implementing different layers
import torch.optim as optim	# For implementing optimizers 
import torch.nn.functional as # For implementing different different activation functions.
```

```python
input_size = 784
num_classes = 10
learning_rate = 0.001
batch_size = 64
num_epochs = 20
```

Load the data efficiently into pytorch DataLoader. 

This will create minibatches for us, as well as shuffle our dataset so that no number is overrepresented in a single batch.  To do this,

```python
from torch.utils.data import DataLoader
train_loader = DataLoader(dataset=torch.tensor(train_dataset.values, dtype=torch.float32), batch_size=batch_size, shuffle=True) 
test_loader = DataLoader(dataset=torch.tensor(test_dataset.values, dtype=torch.float32), batch_size=batch_size, shuffle=True) 
```

Create the network architecture:

We will have two consecutive FC layers with an intermediate activation of ReLu.

```python
class NN(nn.Module):
    def __init__(self, input_size, num_classes):
        super(NN, self).__init__()
        self.fc1 = nn.Linear(input_size, 50)
        self.fc2 = nn.Linear(50, num_classes)
        
    def forward(self, x):
        x = F.relu(self.fc1(x))
        x = self.fc2(x)
        return x
```

Now we can check our model:

```python
model = NN(784, 10)			# Initialize the network. 784 input values and 10 classes for 0 to 9
x = torch.randn(64, 784)	# 64 is the minibatch size
print(model(x).shape)		# Should return [64, 10]
```

- If everything is correct, proceed further towards using this model.

Specify device where you want to do the calculations:

```python
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
```

Specify or Create a loss function, and Specify optimizing algorithm:

```python
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=learning_rate)
```

Train :

For every epoch:

- For every batch

  - Initialize optimizer's gradient storage to zero.

  - Load data to target device
  - Evaluate model output for the data
  - Find Loss
  - Propagate loss backward
  - Perform an Adam Step

```python
for epoch in range(num_epochs):
    for batch_idx, data_all in enumerate(train_loader):
        data = data_all[:, 1:].float()
        targets = data_all[:, 0].to(torch.int64)
        
        data = data.to(device=device)
        targets = targets.to(device=device)
        
        # Forward:
        scores = model(data)
        loss = criterion(scores, targets)
        
        # Backward (In PyTorch, no need to Implement):
        optimizer.zero_grad()
        loss.backward()
        
        # Gradient descent or Adam step:
        optimizer.step()
```



This may take some time dependent on your hardware architecture and number of epochs. 

After training, we can check the accuracy on the test and training set.

1. While checking, we need not calculate the gradients. So we must inform torch to stop doing that while we are checking our model with test data.

```python
def check_accuracy(loader, model, t=1):
    
    if t:
        print("Checking accuracy on Training data")
    else:
        print("Checking accuracy on Test data")        
    
    num_correct = 0
    num_samples = 0
    model.eval()
    
    with torch.no_grad(): # While we are checking, we do not need to compute the gradients. That is unnecessary.
        for data_all in loader:
            x = data_all[:, 1:].float()
            y = data_all[:, 0].to(torch.int64)
            x = x.to(device=device)
            y = y.to(device=device)
            
            scores = model(x)
            _, predictions = scores.max(1) # We are interested in the index of the maximum value in the 2nd dimension.
            num_correct += (predictions == y).sum()
            num_samples += predictions.size(0)
            
        print(f'Got {num_correct} / {num_samples} with accuracy {float(num_correct) / float(num_samples) * 100:.2f}')
            
    model.train()
```

```python
check_accuracy(train_loader, model, t=1)
```

```python
check_accuracy(test_loader, model, t=0)
```



We may also check it by ourselves for some numbers to get full confidence on the prediction of our code. 

We looking at the csv file for some rows and note down its first field which tells us the actual number we want to predict. We pass the remaining 784 fields into our model and check whether they give correct values:

```python
check = torch.tensor(train_dataset.loc[[2, 3]].values, dtype=torch.float32)
print(f'The actual numbers are: {check[:,0]}')

print(f'The predicted numbers are: {model(check[:, 1:]).max(1).indices}')
```


