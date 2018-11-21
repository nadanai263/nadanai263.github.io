---
layout: post
title: "Hello Tensorflow: what is Tensorflow and how do I use it for machine learning?"
date: 2018-11-21
---

Tensorflow is currently a standard and powerful framework to build models for machine learning. Even though its power can already be harnessed using high-level, user-friendly wrappers such as Keras, it is also instructive to dive into the nuts and bolts of how it works. In this tutorial I will show you the most essential elements which make up a Tensorflow model, and how you can quickly build and train a linear regression predictor using these simple parts. I hope to develop these tutorials over time to cover more sophisticated examples. The tutorial contains the following sections:

- [Getting started](##heading-0)
- [1. First steps: defining and evaluating graphs](##heading-1)
* [1.1 Define computation graph](###sub-heading-1)
* [1.2 Evaluate computation graph](###sub-heading-2)
- [2. Feeding graphs](##heading-2)
- [3. Model layers and forward propagation](##heading-3)
- [4. Loss functions, optimisers, and back propagation](##heading-4)
- [5. Putting everything together: linear regression](##heading-5)
- [6. Conclusions](##heading-6)
<!-- toc -->

## Getting started
To get started, make sure you have the following libraries: `tensorflow`, `matplotlib`, `numpy`.
```
pip install tensorflow matplotlib numpy
```

## 1. First steps: defining and evaluating graphs
At the most basic level, computations using tensorflow happen in two steps:
* Define computation graph
* Instantiate a session and evaluate the graph

### 1.1 Define computation graph


```python
a = tf.constant(2.0, dtype=tf.float32)
b = tf.constant(4.0) # also tf.float32 implicitly
total = a + b
print(a,b,total)
```

    Tensor("Const:0", shape=(), dtype=float32) Tensor("Const_1:0", shape=(), dtype=float32) Tensor("add:0", shape=(), dtype=float32)


As you can see, we have created three objects: `a`, `b`, and `total`. They are 'tensor' objects, and we have defined the relationship between them: the sum of the values of `a` and `b` is equal to the value of the object `total`. The graph is however not yet evaluated; to do that we must instantiate a session.

### 1.2 Evaluate computation graph


```python
sess=tf.Session()
print(sess.run(a), sess.run(b), sess.run(total)) # session.run evaluates values of tensors passed as arguments
```

    2.0 4.0 6.0


When we instantiate a session and call the run() method, we finally carry out the computations on the graph. For example, calling sess.run(a) returns the evaluated value of `a`.  

## 2. Feeding graphs

Our first graph was very basic: we initialised our variables `a` and `b` with constants. In general, however, we would like to define a graph and have it accept variable inputs. This is called 'feeding' the graph, and you can do this using 'placeholders'. Placeholders are variables whose values are set during evaluation, rather than during the graph definition stage. The following code block gives the same results as our first graph, but this time using placeholders:


```python
# 1. Define graph
x = tf.placeholder(tf.float32)
y = tf.placeholder(tf.float32)
z = x + y

# 2. Evaluate graph
sess=tf.Session()
print(sess.run(z, feed_dict={x: 2, y: 4}))
```

    6.0


As you can see, when you call the sess.run() method, you now need to pass in a dictionary of values for each placeholder (the 'feed_dict').

## 3. Model layers and forward propagation

A layer in the model is analogous to that in a neural network: it holds trainable parameters. You can build a graph containing layers in exactly the same way as before. However before running the session, you must initialise the variables.


```python
# Data
my_data = np.array([[1,2,3],[3,4,5],[5,6,7]])

# 1. Define graph
x = tf.placeholder(tf.float32, shape=[None,3])
linear_model = tf.layers.Dense(units=1) # Fully connected dense layer with single output
y = linear_model(x)

# 2. Evaluate graph
sess = tf.Session()
init = tf.global_variables_initializer()
sess.run(init)
print(sess.run(y, feed_dict={x: my_data}))
```

    [[3.1232657]
     [6.2646203]
     [9.405975 ]]


Here we first defined `x` which takes in a variable number of 3-element vectors; the first dimension of `x` is set as `None`, which will be determined during evaluation (normally this dimension will correspond to the number of training examples). The linear model defined is a fully connected layer, which takes each 3-element vector of `x` and outputs a single real number `y`. Finally, before evaluation, we initialised the model using random numbers (the tf.global_variables_initializer function does this). Evaluating `y` by feeding in `my_data` results in the forward propagation of the inputs through the model; this generates 3 values for `y`, corresponding to the 3 training examples we fed in.

This example shows a basic forward propagation pass through a single-layer model; hopefully you can see how this might extend to more complex models (you just add more layers...). To finally train the model, we need back propagation: this is automatically implemented in Tensorflow through the use of optimisers.

## 4. Loss functions, optimisers, and back propagation

To enable back propagation and training, we need to define
* a loss function, and
* an optimiser.

The loss function is a measure of how far our model predictions are from the target values. The optimiser tells Tensorflow what changes to make to the parameters to reduce the loss function. The code block below shows how to implement a linear regression on 4 data points:


```python
# Data: x,y pairs of points 
x_data = np.array([[1],[2],[3],[4]])
y_data = np.array([[0],[-1],[-2],[-3]])

# 1. Define graph
x = tf.placeholder(tf.float32, shape=[None,1])
y_true = tf.placeholder(tf.float32, shape=[None,1])
linear_model = tf.layers.Dense(units=1)
y_pred = linear_model(x)

# 1.1 Define loss function: here we use mean squared error
loss = tf.losses.mean_squared_error(labels=y_true, predictions=y_pred)

# 1.2 Define optimiser: here we use gradient descent
optimizer = tf.train.GradientDescentOptimizer(0.1)
train = optimizer.minimize(loss)

# 2. Evaluate graph
sess = tf.Session()
init = tf.global_variables_initializer()
sess.run(init)
```

Like before, we have defined our graph, and passed our data into the model's placeholders through a feed_dict. The model now contains a loss function as well as `train`, which is a call to the optimizer. Each evaluation of `train` will make one gradient descent step, as can be seen by putting the session calls into a loop:


```python
for i in range(5):
    print(sess.run([train, loss], feed_dict = {x : x_data, y_true : y_data}))
```

    [None, 36.441383]
    [None, 16.590256]
    [None, 7.663968]
    [None, 3.6435313]
    [None, 1.8264822]


You can see the decreasing value of the loss function at each step. After a few steps of training, we can evaluate the model predictions:


```python
print(sess.run([y_pred], feed_dict = {x : x_data}))
print(y_data)
```

    [array([[-1.3014169],
           [-2.0794091],
           [-2.8574014],
           [-3.6353936]], dtype=float32)]
    [[ 0]
     [-1]
     [-2]
     [-3]]


## 5. Putting everything together: linear regression

So we are finally ready to put everything together and train our first model, which is a simple linear regression. We can generate a random dataset which is a noisy straight line:


```python
np.random.seed(42)
x_data = np.arange(100)
y_data = x_data*5 + 2 + 20*np.random.randn(100)
plt.plot(x_data, y_data, 'o'); plt.show()
```


<img src="/assets/images/HTF0.png" width="250" alt="me" align="center" hspace="40" vspace="40">


Then we use all the elements we developed above to put together our training function. We'll reshape the data so it's compatible with our placeholders, and then call the training loop. There is one subtle point to make here: the success of training depends quite crucially on the training rate we use. Here I've set the gradient descent rate to 1e-4 which leads to good convergence. If our rates are too high, the training can blow up and not converge; this basically means that you're trying to make steps much bigger than the features in the loss function and end up missing minima. It is therefore generally safer to have rates which are lower; however rates which are too low mean that convergence can become very slow. 

At each training step we can evaluate the loss and plot it. After we finish training we can also evaluate the model predictions with a final sess.run() call. 


```python
# Reshape data to make it compatible with our placeholders
x_data = x_data.reshape(100,1)
y_data = y_data.reshape(100,1)

# 1. Define graph
x = tf.placeholder(tf.float32, shape=[None,1])
y_true = tf.placeholder(tf.float32, shape=[None,1])
linear_model = tf.layers.Dense(units=1)
y_pred = linear_model(x)

# 1.1 Define loss function: here we use mean squared error
loss = tf.losses.mean_squared_error(labels=y_true, predictions=y_pred)

# 1.2 Define optimiser: here we use gradient descent
optimizer = tf.train.GradientDescentOptimizer(0.0001)
train = optimizer.minimize(loss)

# 2. Evaluate graph
sess = tf.Session()
init = tf.global_variables_initializer()
sess.run(init)

# Train

for i in range(20):
    _,loss_value = sess.run((train,loss), feed_dict = {x : x_data, y_true : y_data})
    
    if i==0:
        losscurve=np.array([loss_value])
    else:
        losscurve=np.append(losscurve,loss_value)
        
plt.semilogy(losscurve); 
plt.xlabel('Iterations'); 
plt.ylabel('Loss'); 
plt.title('Training curve')
plt.show()

y_out = sess.run(y_pred, feed_dict = {x : x_data})
plt.plot(x_data, y_data, 'o'); 
plt.plot(x_data, y_out, '-', lw=2); 
plt.title('Model predictions'); 
plt.show()
```
<img src="/assets/images/HTF1.png" width="250" alt="me" align="center" hspace="40" vspace="40">
<img src="/assets/images/HTF2.png" width="250" alt="me" align="center" hspace="40" vspace="40">

## 6. Conclusions

I hope you enjoyed this short introduction to Tensorflow. Please stay tuned for more sophisticated examples! A jupyter notebook is available for this tutorial on my [GitHub repository](https://github.com/nadanai263/datasciportfolio).
