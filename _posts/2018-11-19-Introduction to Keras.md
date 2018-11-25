---
layout: post
title: "Introduction to Keras"
date: 2018-11-19
description: Keras is a high-level wrapper for Tensorflow and Theano, both of which are powerful machine learning frameworks, and for many people it is their first contact with deep learning. In this first tutorial, I present the most basic introduction to Keras, and develop a code framework which we'll recycle in later tutorials covering more advanced topics. 
---

Keras is a high-level wrapper for Tensorflow and Theano, both of which are powerful machine learning frameworks, and for many people it is their first contact with deep learning. In this first tutorial, I present the most basic introduction to Keras, and develop a code framework which we'll recycle in later tutorials covering more advanced topics. 

Keras provides a quick and easy way to define and train neural network models. It does this by wrapping around powerful frameworks such as Tensorflow and Theano, which are optimised to carry out the linear algebraic computations necessary for machine learning. We won't go into details or theory here; that's the subject of very many excellent tutorials around the web. In this tutorial, I will present the bare-bones code necessary to get a model up and running. We'll iterate over this code framework in subsequent tutorials. But first, let's do some setting up.

The tutorial contains the following sections:

* TOC
{:toc}
<!-- toc -->

### 1. Getting started
First, we need the following libraries: `keras`, `python-mnist`, `numpy`.
```
pip install keras python-mnist numpy
```

### 2. Loading data

Loading and preparing data is a big topic which is covered in more detail elsewhere. Today we will use the `python-mnist` package and load the MNIST dataset, which is a standard 'hello-world'-type dataset used as a reference for deep learning models. It consists of 60,000 training and 10,000 test examples of 28x28 images of handwritten digits; the objective of the model is to predict which digit an image corresponds to. It is thus a 10-class classification problem. 

First we load all images into memory as 28x28 numpy arrays. These 8-bit images are then normalised by dividing by 255 to put their values between 0 and 1, and then flattened into 1D vectors of length 784. Finally, we convert the categorical (y) labels to one-hot binary class matrices. The following code block does this:


```python
NUM_CLASSES = 10

# Set seed for reproducible results
np.random.seed(1)

# 1. Load data using MNIST helper functions 
# and convert to flattened, normalised numpy arrays

mndata = MNIST('./MNIST_data',gz=True)
images_train, labels_train = mndata.load_training()
images_test, labels_test = mndata.load_testing()
x_train = np.asarray(images_train)/255
y_train = np.asarray(labels_train)
x_test = np.asarray(images_test)/255
y_test = np.asarray(labels_test)

# 2. Convert class vectors from digit labels to binary class matrices (N x 10)
y_train = keras.utils.to_categorical(y_train, NUM_CLASSES)
y_test = keras.utils.to_categorical(y_test, NUM_CLASSES)

print(x_train.shape[0], 'Number of training samples')
print(x_test.shape[0], 'Number of test samples')
print('x_train shape:', x_train.shape)
print('y_train shape:', y_train.shape)
print('x_test shape:', x_test.shape)
print('y_test shape:', y_test.shape)
```

    60000 Number of training samples
    10000 Number of test samples
    x_train shape: (60000, 784)
    y_train shape: (60000, 10)
    x_test shape: (10000, 784)
    y_test shape: (10000, 10)


The next part shows an example of the original image, a flattened image, the original label, and finally the one-hot representation of the label, just so we have a glimpse into what the data looks like.


```python
index = 5
print('Example image:')
plt.imshow(x_train[index].reshape(28,28)); plt.show()
print('Flattened image:')
plt.figure(figsize=(8,0.2));
sns.heatmap(x_train[index].reshape(1,784), cbar=False); plt.show()
print('Corresponding label:')
print(np.asarray(labels_train)[index])
print('One-hot label:')
print(y_train[index])
```

    Example image:


<img src="/assets/images/IK_6_1.png" width="150" alt="me" align="center" hspace="40" vspace="40">


    Flattened image:



<img src="/assets/images/IK_6_3.png" width="400" alt="me" align="center" hspace="40" vspace="40">



    Corresponding label:
    2
    One-hot label:
    [0. 0. 1. 0. 0. 0. 0. 0. 0. 0.]


### 3. Basic framework for using Keras

Here is where the Keras part begins. At its simplest, the four steps we need to operate Keras are:

1. Define the model graph
2. Compile the model
3. Train the model
4. Evaluate our results

These are similar to the steps in Tensorflow, but with fewer intricacies at each step. 

#### 3.1 Defining the model

There are two ways to define a model in Keras, using either a Sequential or a Functional approach. The Sequential approach generates the model layer-by-layer, while in the Functional approach you define how the layers are linked. It's thus easy to make simple models using the Sequential approach, but more complex models would require a Functional definition. Here I show how to make a one-layer neural network which takes the input vector, fully connects it to a single 10-neuron layer, and applies a softmax activation to generate the output.


```python
# Define model graph using Sequential API
 
model_name = 'FC1'
model = Sequential()
model.add(Dense(NUM_CLASSES, input_dim=x_train.shape[1], name='D1')) # Add first dense layer
model.add(Activation('softmax',name='Output')) # Add softmax activation 
model.summary()
```

    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    D1 (Dense)                   (None, 10)                7850      
    _________________________________________________________________
    Output (Activation)          (None, 10)                0         
    =================================================================
    Total params: 7,850
    Trainable params: 7,850
    Non-trainable params: 0
    _________________________________________________________________


First, we define `model` to be a `Sequential()` object. Then, we merely add layers to this object. The first layer requires the input dimensions to be specified. Each layer is given a name; normally I always name my layers, as this is useful for saving and loading models and weights (shown below). Finally, the structure of the model can be shown using the `summary()` method.

The following shows how to generate the same model using the Functional approach. As you can see, here you define connections between layers such as Input, Dense, and Activation; then, you build the model using the `Model()` call. As before, the Input layer requires you to specify the input dimensions; each layer is again given a name. While this approach looks slightly more complicated, it's my preferred approach for building models in Keras as it offers much more flexibility.


```python
# Define model graph using Functional API

model_name = 'FC1'
x_input = Input(shape=(x_train.shape[1],), name='Input1')
x = Dense(NUM_CLASSES, name='D1')(x_input)
x = Activation('softmax', name='Output')(x)
model = Model(inputs=x_input, outputs=x)
model.summary()

```

    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    Input1 (InputLayer)          (None, 784)               0         
    _________________________________________________________________
    D1 (Dense)                   (None, 10)                7850      
    _________________________________________________________________
    Output (Activation)          (None, 10)                0         
    =================================================================
    Total params: 7,850
    Trainable params: 7,850
    Non-trainable params: 0
    _________________________________________________________________


#### 3.2 Compiling the model

With our model built, we proceed to compile it. Here, the compilation requires you to define the loss function and optimiser, just like in [Tensorflow](/2018/11/21/Hello-Tensorflow). The loss function determines how far the model predictions are from the training labels; the optimiser determines how you minimise that loss. Finally, the metrics argument specifies what quantities you evaluate during training and testing. 


```python
# Compile the model
adam=Adam(lr=0.001)
model.compile(loss=keras.losses.categorical_crossentropy,
              optimizer=adam,
              metrics=['accuracy'])
```

#### 3.3 Training

Finally, we train the model. To do this, we call the `fit()` method, which returns a `history` object containing a record of the training process, which we'll be able to access later. For our first basic example, we will feed in all the training data. The training proceeds using a minibatch gradient descent, where each batch has 32 examples. We train for 10 epochs, or passes through the complete dataset. Finally, the verbose argument turns on the console output of the training process.


```python
# Train the model
history=model.fit(x_train, y_train, batch_size=32, epochs=10, verbose=1)
```

    Epoch 1/10
    60000/60000 [==============================] - 4s 59us/step - loss: 0.4678 - acc: 0.8776
    Epoch 2/10
    60000/60000 [==============================] - 3s 56us/step - loss: 0.3044 - acc: 0.9160
    Epoch 3/10
    60000/60000 [==============================] - 3s 45us/step - loss: 0.2834 - acc: 0.9203
    Epoch 4/10
    60000/60000 [==============================] - 3s 43us/step - loss: 0.2734 - acc: 0.9227
    Epoch 5/10
    60000/60000 [==============================] - 3s 43us/step - loss: 0.2665 - acc: 0.9262
    Epoch 6/10
    60000/60000 [==============================] - 3s 52us/step - loss: 0.2618 - acc: 0.9271
    Epoch 7/10
    60000/60000 [==============================] - 4s 63us/step - loss: 0.2584 - acc: 0.9281
    Epoch 8/10
    60000/60000 [==============================] - 3s 53us/step - loss: 0.2554 - acc: 0.9295
    Epoch 9/10
    60000/60000 [==============================] - 3s 53us/step - loss: 0.2527 - acc: 0.9303
    Epoch 10/10
    60000/60000 [==============================] - 3s 54us/step - loss: 0.2505 - acc: 0.9311


#### 3.4 Evaluate our results

Once our model is trained, we can evaluate the model on our test data: the `evaluate()` method returns loss and accuracy scores:


```python
# Evaluate
score = model.evaluate(x_test,y_test, verbose=1)
print('Test Loss:', score[0])
print('Test Accuracy:', score[1])
```

    10000/10000 [==============================] - 0s 33us/step
    Test Loss: 0.2638074106231332
    Test Accuracy: 0.9281


We see that our most basic 10-node network, containing 7,850 parameters, performs with 92% accuracy on the held-out test set: this is the expected standard result, and is far from the maximum possible [state-of-the-art](https://www.kaggle.com/c/digit-recognizer/discussion/61480#latest-427248) value of 99.8%. 

### 4. Useful functions for Keras workflow

I hope you've seen from the discussion above how easy it is to build, train, and test a model using Keras. As the problem and models become more complex, we will need to introduce tricks at each step, but the basic framework will remain unchanged. In the rest of this tutorial I will show some functions which will already be useful for our current approach:

* Saving and loading models: essential tasks 
* Plotting training history: useful to visualise and debug the training process
* Automatic cross-validation: an out-of-the-box method to perform cross-validation with a dev set. More sophisticated techniques will be discussed elsewhere.

#### 4.1 Saving and loading models

Once we have trained our model, we'd like to save both the architecture and weights. The architecture is saved as a json file, while the weights are saved as an hdf5 file. 


```python
# Saving model and weights
DIR = './Keras_results/'
if not os.path.exists(DIR):
    os.mkdir(DIR)

# 1. Save model architecture as json
json_string = model.to_json()
open(DIR + model_name + '_arch.json', 'w').write(json_string)

# 2. Save model weights as hdf5
model.save_weights(DIR + model_name + '_weights.h5')
```

To load a model we can run the steps in reverse: load the architecture from a json file, and load the weights from an h5 file. We can alternatively rebuild the model as shown above, and then feed that directly with the weights. The weights are assigned to the correct layers if the argument `by_name=True` is used.


```python
# Loading model and weights

# 1. Load model architecture from json
model = keras.models.model_from_json(open(DIR + model_name + '_arch.json').read())

# 2. Load model weights from hdf5
model.load_weights(DIR + model_name + '_weights.h5', by_name=True)
```

#### 4.2 Plotting training history

We can interrogate the history object returned by the `fit()` method to plot our training history:


```
fig, ax = plt.subplots(1, 2, figsize=(12,4));
ax[0].set_title('Loss'); ax[0].set_xlabel('Epochs');
ax[0].plot(history.epoch, history.history["loss"], label="Train loss");
ax[1].set_title('Accuracy'); ax[1].set_xlabel('Epochs');
ax[1].plot(history.epoch, history.history["acc"], label="Train accuracy");
ax[0].legend();
ax[1].legend();
```


<img src="/assets/images/IK_33_0.png" width="75%" alt="me" align="center" hspace="40" vspace="40">



#### 4.3 Cross-validation using a dev set

While cross-validation will be discussed in more detail in another tutorial, Keras already offers an automatic way to train with cross-validation, using the `validation_split` argument of the `fit()` method. The value tells Keras to hold out a proportion of the training set and use that as a dev set, allowing us to estimate our model's predictive performance before it sees the held-out test set. 


```python
# Train the model with cross-validation
history=model.fit(x_train, y_train, batch_size=32, epochs=10, verbose=1, validation_split=0.1)
```

    Train on 54000 samples, validate on 6000 samples
    Epoch 1/10
    54000/54000 [==============================] - 2s 46us/step - loss: 0.4885 - acc: 0.8712 - val_loss: 0.2728 - val_acc: 0.9265
    Epoch 2/10
    54000/54000 [==============================] - 3s 55us/step - loss: 0.3138 - acc: 0.9125 - val_loss: 0.2450 - val_acc: 0.9343
    Epoch 3/10
    54000/54000 [==============================] - 2s 41us/step - loss: 0.2916 - acc: 0.9183 - val_loss: 0.2347 - val_acc: 0.9352
    Epoch 4/10
    54000/54000 [==============================] - 2s 45us/step - loss: 0.2803 - acc: 0.9217 - val_loss: 0.2291 - val_acc: 0.9367
    Epoch 5/10
    54000/54000 [==============================] - 2s 45us/step - loss: 0.2731 - acc: 0.9234 - val_loss: 0.2278 - val_acc: 0.9388
    Epoch 6/10
    54000/54000 [==============================] - 2s 46us/step - loss: 0.2683 - acc: 0.9249 - val_loss: 0.2276 - val_acc: 0.9377
    Epoch 7/10
    54000/54000 [==============================] - 3s 47us/step - loss: 0.2644 - acc: 0.9262 - val_loss: 0.2230 - val_acc: 0.9387
    Epoch 8/10
    54000/54000 [==============================] - 3s 53us/step - loss: 0.2610 - acc: 0.9274 - val_loss: 0.2234 - val_acc: 0.9403
    Epoch 9/10
    54000/54000 [==============================] - 3s 52us/step - loss: 0.2582 - acc: 0.9285 - val_loss: 0.2233 - val_acc: 0.9397
    Epoch 10/10
    54000/54000 [==============================] - 3s 63us/step - loss: 0.2563 - acc: 0.9291 - val_loss: 0.2228 - val_acc: 0.9403



```python
# Plot training history 
fig, ax = plt.subplots(1, 2, figsize=(12,4));
ax[0].set_title('Loss'); ax[0].set_xlabel('Epochs');
ax[0].plot(history.epoch, history.history["loss"], label="Train loss");
ax[0].plot(history.epoch, history.history["val_loss"], label="Validation loss");
ax[1].set_title('Accuracy'); ax[1].set_xlabel('Epochs');
ax[1].plot(history.epoch, history.history["acc"], label="Train accuracy");
ax[1].plot(history.epoch, history.history["val_acc"], label="Validation accuracy");
ax[0].legend();
ax[1].legend();
```


<img src="/assets/images/IK_37_0.png" width="75%" alt="me" align="center" hspace="40" vspace="40">


### 5. Conclusions

In this tutorial I showed a basic framework in Keras, where you define, compile, train, and test a model on a toy dataset. The basic framework remains unchanged even as the problem and model complexity increases. I hope to show more things you can do with Keras soon. 

A jupyter notebook is available for this tutorial on my [GitHub repository](https://github.com/nadanai263/datasciportfolio). The definitive guide to Keras is the documentation available on their [homepage](https://keras.io).
