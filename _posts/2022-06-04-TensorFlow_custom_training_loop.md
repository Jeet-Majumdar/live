---
layout: post
title: TensorFlow - Writing Custom Training Loop
categories: [Tech]
---


- Create the data structure using:

  ```python
  train_dataset = tf.data.Dataset.from_tensor_slices((x_train, y_train))
  train_dataset = train_dataset.shuffle(buffer_size=1024).batch(batch_size)
  ```

  Ingredients: `(x_train, y_train)` 

- Create the model:

  ```python
  inputs = keras.Input(shape=(784,), name="digits")
  x1 = layers.Dense(64, activation="relu")(inputs)
  x2 = layers.Dense(64, activation="relu")(x1)
  outputs = layers.Dense(10, name="predictions")(x2)
  model = keras.Model(inputs=inputs, outputs=outputs)
  ```

- Instantiate Optimizer and Loss function:

  ```python
  optimizer = keras.optimizers.SGD(learning_rate=1e-3) 
  loss_fn = keras.losses.SparseCategoricalCrossentropy(from_logits=True)
  ```

- Define Training Loop:

  > Here's our training loop:
  >
  > - We open a `for` loop that iterates over epochs
  > - For each epoch, we open a `for` loop that iterates over the dataset, in batches
  > - For each batch, we open a `GradientTape()` scope
  > - Inside this scope, we call the model (forward pass) and compute the loss
  > - Outside the scope, we retrieve the gradients of the weights of the model with regard to the loss
  > - Finally, we use the optimizer to update the weights of the model based on the gradients

  ```python
  epochs = 2
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
  
  

### Others:

- To use custom learning_rate, see [this](https://www.tensorflow.org/api_docs/python/tf/keras/optimizers/schedules/LearningRateSchedule). 
- Once tape.gradient() is called, the memory of GradientTape is cleaned.

### Ref:

1. From [Tensorflow Doc](https://www.tensorflow.org/guide/keras/writing_a_training_loop_from_scratch)
