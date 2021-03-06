TensorFlow
===========================================

The Basic Workflow
-------------------------------------------

A multi-classification solver using `n` records and `m` features to predict
`p` labels. Using a multi-logistic (softmax) classifier.

```{python}
import tensorflow as tf
graph = tf.Graph()

with graph.as_default():
  # define network structure
  tf_train_dataset = tf.constant(train_dataset)
  tf_train_labels  = tf.constant(train_labels)
  tf_valid_dataset = tf.constant(valid_dataset)

  weights = tf.Variable(tf.truncate_normal([m, p]))
  biases  = tf.Variable(tf.zeros([p]))

  logits = tf.matmul(tf_train_dataset, weights) + biases

  loss = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(logtis, targets))
  # use 0.5 as the learning rate
  optimizer = tf.train.GradientDescentOptimizer(0.5).minimize(loss)  

  train_prediction = tf.nn.softmax(...)
  valid_prediction = tf.nn.softmax(...)

with tf.Session(graph=graph):
  tf.initialize_all_variables().run()
  for step in range(1000):
    _, l, prediction = session.run([optimizer, loss, train_prediction])
    valid_pred = valid_prediction.eval()
```

### Stochastic Gradient Descent (SDG)
SDG is faster, which trains on mini-batches - updating all unknowns using a subset of data.

```{python}
batch_size = 128

graph = tf.Graph()
with graph.as_default():

  # Input data. For the training data, we use a placeholder that will be fed
  # at run time with a training minibatch.
  tf_train_dataset = tf.placeholder(tf.float32, shape=(batch_size, image_size * image_size))
  tf_train_labels  = tf.placeholder(tf.float32, shape=(batch_size, num_labels))
  tf_valid_dataset = tf.constant(valid_dataset)
  tf_test_dataset  = tf.constant(test_dataset)

  # Variables.
  weights = tf.Variable(tf.truncated_normal([image_size * image_size, num_labels]))
  biases  = tf.Variable(tf.zeros([num_labels]))

  # Training computation.
  logits = tf.matmul(tf_train_dataset, weights) + biases
  loss = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(logits, tf_train_labels))

  # Optimizer.
  optimizer = tf.train.GradientDescentOptimizer(0.5).minimize(loss)

  # Predictions for the training, validation, and test data.
  train_prediction = tf.nn.softmax(logits)
  valid_prediction = tf.nn.softmax(tf.matmul(tf_valid_dataset, weights) + biases)
  test_prediction = tf.nn.softmax(tf.matmul(tf_test_dataset, weights) + biases)

# Then Run it

num_steps = 3001

with tf.Session(graph=graph) as session:
  tf.initialize_all_variables().run()

  for step in range(num_steps):
    offset = (step * batch_size) % (train_labels.shape[0] - batch_size)

    # Generate a minibatch.
    batch_data   = train_dataset[offset:(offset + batch_size), :]
    batch_labels = train_labels[offset:(offset + batch_size), :]

    feed_dict = {tf_train_dataset : batch_data, tf_train_labels : batch_labels}
    _, l, predictions = session.run([optimizer, loss, train_prediction], feed_dict=feed_dict)
    if (step % 500 == 0):
      # do something with
      valid_prediction.eval()
```

Useful Functions
---------------------------------------------

### Essentials
```{python}
tf.Graph()
tf.Graph().as_default()  # used in the with block
tf.Session(graph=graph)  # used in the with block
session.run([optimizer, loss, train_prediction], feed_dict={})
```

### Network Building Blocks
```{python}
tf.constant()
tf.Variable()     # capitalized
tf.placeholder()
tf.nn.relu()      # simplest piecewise linear function
tf.nn.softmax()   # make predictions
```

### Loss Functions
```{python}
tf.softmax_cross_entropy_with_logits(x, y)
```

### Operations
```{python}
tf.matmul(x, y)
tf.reduce_mean(x)
```

### Variable Initialization
```{python}
tf.initialize_all_variables()  # this is tf, not session
tf.truncated_normal([n, m])
```

### optimizer
```{python}
tf.train.GradientDescentOptimizer(rate).minimize(loss)
```

Notes
---------------------------------------------------------
Never Call `train_prediction.eval()`, not sure why but the program crashes.
