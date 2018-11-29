---
layout: post
title: "Cross-validation"
date: 2018-11-26
description: The aim of cross-validation is to hold out a part of the training set, which is then used to evaluate the model's performance. Because this held-out data is not seen by the model during training, the evaluation provides an estimate of how well the model will generalise to the unseen test set. This tutorial shows how to implement cross-validation using Keras.
---

The aim of cross-validation is to hold out a part of the training set, which is then used to evaluate the model's performance. Because this held-out data is not seen by the model during training, the evaluation provides an estimate of how well the model will generalise to the unseen test set. This tutorial shows how to implement cross-validation using Keras.

During training, it is important to assess how well the model performs on the training set, as well as on the cross-validation (sometimes known as the validation or 'dev') set. The first metric is an indication of the model's bias, while the second provides an indication of its variance. Different techniques are used to improve bias and variance, so knowing the relative contributions of each is essential to successfully tune the model. Today we'll look at increasingly complex implementations of cross-validation. Let's get started!

The tutorial contains the following sections:

* TOC
{:toc}
<!-- toc -->

---

### 1. Load data, build  and compile model

We'll first load the MNIST data and build our model exactly as shown in a previous Keras tutorial.


```python
NUM_CLASSES = 10

# Set seed for reproducible results
np.random.seed(1)

# 1. Load data and convert to flattened, normalised numpy arrays

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

# 2. Define model graph using Functional API

model_name = 'FC1'
x_input = Input(shape=(x_train.shape[1],), name='Input1')
x = Dense(NUM_CLASSES, name='D1')(x_input)
x = Activation('softmax', name='Output')(x)
model = Model(inputs=x_input, outputs=x)
model.summary()

# Compile the model
adam=Adam(lr=0.001)
model.compile(loss=keras.losses.categorical_crossentropy,
              optimizer=adam,
              metrics=['accuracy'])
```

    60000 Number of training samples
    10000 Number of test samples
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


Now, we have to process the data to generate our held-out dev set. The first way we can do it is completely manually.

---

### 2. Manually generating dev set

Manually generating the dev set is useful so that we can see exactly what's going on. First, we generate an array which indexes the data, `inds`. We can then shuffle the array in-place. Finally, we carve the initial training data into a train and dev portion (with the relative size of the dev set determined by `percent_holdout`) by slicing `inds` appropriately. Our two new arrays `dev_inds` and `train_inds` can now be used to index our dev and training sets, respectively.


```python
# Set seed for reproducible results
np.random.seed(1)

# Create array of indices
inds = np.arange(y_train.shape[0])
print('Index array', inds[:10])

# Shuffle indices in-place
np.random.shuffle(inds) 
print('Shuffled index array', inds[:10])

# Shuffle data and generate dev set indices
percent_holdout = 0.1 # percentage of data to hold out
dev_inds = inds[int((1-percent_holdout)*len(inds)):] # Dev set
train_inds = inds[:int((1-percent_holdout)*len(inds))] # Training set 

# Look at final distributions
plt.hist(train_inds); 
plt.hist(dev_inds); plt.show()

# Set train and dev sets using our sliced indices
x_train_subset = x_train[train_inds]
y_train_subset = y_train[train_inds]
x_dev = x_train[dev_inds]
y_dev = y_train[dev_inds]

print('Number of training examples', len(train_inds))
print('Number of dev examples', len(dev_inds))
```

    Index array [0 1 2 3 4 5 6 7 8 9]
    Shuffled index array [15281 21435 44536 13518 47529 53560 41094  6060  9358 49039]



<img src="/assets/images/CV_8_1.png" width="50%" alt="me" align="center" hspace="40" vspace="40">


    Number of training examples 54000
    Number of dev examples 6000


We can also inspect the distribution of our data (according to index) to make sure that the slicing was done properly. 

Finally, when we train the model we can have to specify our new dev set as well as the reduced subset of training examples:


```python
# Train the model with cross-validation
history = model.fit(x_train_subset, y_train_subset, 
                  validation_data=(x_dev, y_dev),
                  batch_size=32, epochs=50, verbose=0)
```


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


<img src="/assets/images/CV_11_0.png" width="80%" alt="me" align="center" hspace="40" vspace="40">


Inspecting the training curve shows us that while the training loss decreases monotonically, at some point the validation loss stops improving and can in fact start to increase. At that point, the model is no longer generalising well to the held-out dev set, and further training merely fits the model more accurately to the training data. This is known as overfitting. 

We can evaluate the loss and accuracy for the train, dev, and test sets, and observe that the dev set does a pretty good job of predicting the test set scores:


```python
# Evaluate
score = model.evaluate(x_train_subset,y_train_subset, verbose=0)
print('Train Loss:', score[0])
print('Train Accuracy:', score[1])

score = model.evaluate(x_dev,y_dev, verbose=0)
print('Dev Loss:', score[0])
print('Dev Accuracy:', score[1])

score = model.evaluate(x_test,y_test, verbose=0)
print('Test Loss:', score[0])
print('Test Accuracy:', score[1])
```

    Train Loss: 0.22134595866225384
    Train Accuracy: 0.9370925925925926
    Dev Loss: 0.28396812576055525
    Dev Accuracy: 0.922
    Test Loss: 0.28355130719542504
    Test Accuracy: 0.9248


To look in detail at how well our dev set represents the test set, we can evaluate the model on not only the train and dev sets, but also the test set after each epoch. The way to do this is to define a new callback, which calls the `evaluate()` function at the end of each epoch.

Normally, evaluating the test set like this is not done during training (the test set is only evaluated once after training is finished). But here for illustration, you can see that the dev set does a pretty good job of predicting the test set performance, at least in this toy example.


```python
# Callback to evaluate test set at each epoch
class TestCallback(keras.callbacks.Callback):
    def __init__(self, test_data):
        self.test_data = test_data
        self.testloss = []
        self.testacc = []

    def on_epoch_end(self, epoch, logs={}):
        x, y = self.test_data
        loss, acc = self.model.evaluate(x, y, verbose=0)
        self.testloss.append(loss)
        self.testacc.append(acc)
        
# Compile the model
adam = Adam(lr=0.001)
model.compile(loss=keras.losses.categorical_crossentropy,
              optimizer=adam,
              metrics=['accuracy'])

# Train the model with cross-validation
testhistory = TestCallback((x_test,y_test))
history = model.fit(x_train_subset, y_train_subset, 
                  validation_data=(x_dev, y_dev),
                  batch_size=32, epochs=10, verbose=0, 
                  callbacks=[testhistory])
```


```python
# Plot training history 
fig, ax = plt.subplots(1, 2, figsize=(12,4));
ax[0].set_title('Loss'); ax[0].set_xlabel('Epochs');
ax[0].plot(history.epoch, history.history["loss"], label="Train loss");
ax[0].plot(history.epoch, history.history["val_loss"], label="Validation loss");
ax[0].plot(history.epoch, testhistory.testloss, label="Test loss");
ax[1].set_title('Accuracy'); ax[1].set_xlabel('Epochs');
ax[1].plot(history.epoch, history.history["acc"], label="Train accuracy");
ax[1].plot(history.epoch, history.history["val_acc"], label="Validation accuracy");
ax[1].plot(history.epoch, testhistory.testacc, label="Test accuracy");
ax[0].legend();
ax[1].legend();
```


<img src="/assets/images/CV_16_0.png" width="50%" alt="me" align="center" hspace="40" vspace="40">

---

### 3. Automatically generating the dev set

#### 3.1 Using the built-in Keras function

As shown in the Keras tutorial, you can also use the built-in Keras function to generate a held-out test set, by calling `fit()` with the `validation_split` argument. Here, the first 10% of the training data (unshuffled) is used as the dev set:


```python
# Train the model with cross-validation
history = model.fit(x_train, y_train, batch_size=32, epochs=10, verbose=1, validation_split=0.1)
```

#### 3.1 Using the scikit-learn function

The `scikit-learn` library also supplies a handy function called `train_test_split()` which allows you to generate a dev set in the same way as above.


```python
# Generate dev set using scikit-learn split function
seed = 1
x_train_skl, x_dev_skl, y_train_skl, y_dev_skl = train_test_split(x_train, y_train, 
                                                                  test_size=0.1, 
                                                                  random_state=seed)
```


```python
# Train the model with cross-validation
history = model.fit(x_train_skl, y_train_skl, 
                  validation_data=(x_dev_skl, y_dev_skl),
                  batch_size=32, epochs=10, verbose=0)

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


<img src="/assets/images/CV_24_0.png" width="80%" alt="me" align="center" hspace="40" vspace="40">

---

### 4. k-fold cross-validation

To get a really accurate estimate of the model's generalisibility, we can perform the cross-validation multiple times with different dev sets: in k-fold cross-validation, the training set is partitioned into k subsets, with one of the subsets held out as a dev set, while the other k-1 subsets are used for training. 

We can do the k-fold splitting manually, or use another scikit-learn function, `StratifiedKFold()`. Stratification means that the splitting is done such that the proportion of samples in each class are preserved (i.e. the distribution of data does not change) in each split. More information can be found in the [documentation](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.StratifiedKFold.html). One nuance is that `StratifiedKFold()` takes in labels of categorical values instead of one-hot vectors; thus our 10-dimensional y vectors must be reconverted into a 1-dimensional vector using `np.argmax()`. 

To run a 10-fold cross-validation, we must build, compile, train, and evaluate the model 10 times, which can be done using a for loop. The results are shown below:


```python
seed = 1
skf = StratifiedKFold(n_splits=10, shuffle=True, random_state=seed)
cvscores = []

for train_inds, dev_inds in skf.split(x_train, np.argmax(y_train,axis=1)):
    # 1. Build model
    model_name = 'FC1'
    x_input = Input(shape=(x_train.shape[1],), name='Input1')
    x = Dense(NUM_CLASSES, name='D1')(x_input)
    x = Activation('softmax', name='Output')(x)
    model = Model(inputs=x_input, outputs=x)
    # 2. Compile model
    adam=Adam(lr=0.001)
    model.compile(loss=keras.losses.categorical_crossentropy,
                  optimizer=adam,
                  metrics=['accuracy'])
    # 3. Fit the model
    history = model.fit(x_train[train_inds], y_train[train_inds], 
                        batch_size=32, epochs=10, verbose=0)
    # 4. Evaluate the model
    scores = model.evaluate(x_train[dev_inds], y_train[dev_inds], verbose=0)
    print("%s: %.2f%%" % (model.metrics_names[1], scores[1]*100))
    cvscores.append(scores[1] * 100)
print("%.2f%% (+/- %.2f%%)" % (np.mean(cvscores), np.std(cvscores)))
```

    acc: 92.36%
    acc: 92.39%
    acc: 92.25%
    acc: 91.47%
    acc: 92.48%
    acc: 92.48%
    acc: 93.25%
    acc: 92.90%
    acc: 92.23%
    acc: 91.83%
    92.36% (+/- 0.47%)


The result of this evaluation gives the most accurate prediction for the model's generalisability to unseen data; however this approach is clearly computationally intensive and is not normally used in large deep learning models.

---

### 5. Conclusions

In this tutorial I have shown a few different ways of implementing cross-validation using Keras. Cross-validation is a method for estimating how well the model generalises to unseen data, and is crucial for evaluating and tuning the model's performance. Some of the material here was taken from this excellent [tutorial](https://machinelearningmastery.com/evaluate-performance-deep-learning-models-keras/).

A jupyter notebook is available for this tutorial on my [GitHub repository](https://github.com/nadanai263/datasciportfolio).
