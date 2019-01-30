---
layout: page
title: "Metrics for binary classification"
date: 2018-11-24
description: In this tutorial we will look at how to evaluate a classifier. To keep things simple, we won't look at details of the classifier itself, but we'll assume that we already have a working model, and consider how we might evaluate its performance. I'll explain some useful metrics including precision, recall, and the F1 score.
---

In this tutorial we will look at how to evaluate a classifier. To keep things simple, we won't look at details of the classifier itself, but we'll assume that we already have a working model, and consider how we might evaluate its performance. I'll explain some useful metrics including precision, recall, and the F1 score.

Let's get started!

The tutorial contains the following sections:

* TOC
{:toc}
<!-- toc -->

---

### 1. Binary classification and the decision boundary

To begin, let's suppose we have some data distributed along a one dimensional coordinate. The data fall into two unequal populations, which we'll call 'true' and 'false', and each population is normally distributed about some mean value. Setting the 0 value of our coordinate axis to lie equidistant from the two means, we have the following situation:


```python
# Show two normal distributions

np.random.seed(42)
nsamplestrue  = 2500
nsamplesfalse = 10000
mean = 1
distA = np.random.randn(nsamplestrue)+mean
distB = np.random.randn(nsamplesfalse)-mean
plt.hist(distA, bins=100, alpha=0.5, label='True');
plt.hist(distB, bins=100, alpha=0.5, label='False');
plt.axvline(x=mean, lw=1, color='k', alpha=0.5);
plt.axvline(x=-mean, lw=1, color='k', alpha=0.5);
plt.xlabel('Coordinate');
plt.ylabel('Counts');
plt.legend()
plt.show()
```


<img src="/assets/images/MBC_4_0.png" width="50%" alt="me" align="center" hspace="40" vspace="40">


With these data, a classifier model should predict, given a new data point with some value of the coordinate, whether it belongs to the True or the False population. This type of problem is known as a binary classification, and is extremely common: for example, the two populations could represent the result of a medical test, or the presence of some signal above a noisy background. 

An easy way to do this is for the model to determine a 'decision boundary' or threshold: points located to the left of the boundary belong to the False category, and those on the right of the boundary belong to True. This is shown below:


```python
# Show decision boundary 

plt.hist(distA, bins=100, alpha=0.5, label='True');
plt.hist(distB, bins=100, alpha=0.5, label='False');
plt.axvline(x=mean, lw=1, color='k', alpha=0.5);
plt.axvline(x=-mean, lw=1, color='k', alpha=0.5);
plt.axvline(x=0, lw=1, color='k', alpha=1, linestyle='--', label='Boundary');
plt.xlabel('Coordinate');
plt.ylabel('Counts');
plt.legend()
plt.show()
```


<img src="/assets/images/MBC_6_0.png" width="50%" alt="me" align="center" hspace="40" vspace="40">



When we train a model, what we're doing is letting it come up with the location of this boundary. We'll leave the discussion of training to another tutorial, but I hope you can already see that different models may come up with different decision boundaries. We can therefore simulate the effects of changing models (or model predictions) by moving the decision boundary around.

The boundary shown here is located exactly in the middle between the two means. While this may seem like a sensible place to put it, it's not necessarily the optimum location. What does 'optimum' mean? 

The rest of this tutorial will be devoted to answering that question. In general there is no 'best' solution; we can only define objective measures, or metrics, of the model prediction, and then use those measures to compare between different models. Different problems will require prioritising different metrics. Let's see what this means.

---

### 2. Metrics for model evaluation

#### 2.1 Minimum error

In a classification problem, as well as making true positive (TP) and true negative (TN) predictions, a model may also make false positive (FP) and false negative (FN) predictions. In terms of the figure, false positives are orange points located to the right of the decision boundary, while false negatives are blue points to the left. 

A useful metric of our model performance might be the total number of false predictions. According to this metric, a more optimal model would be one that we minimises the number of false predictions. The result is shown below, and you can see that the location which minimises the overall error is where the two population distributions cross.


```python
fig, ax = plt.subplots(1,2, figsize=(10,3))
ax[0].hist(distA, bins=100, alpha=0.5);
ax[0].hist(distB, bins=100, alpha=0.5);
ax[0].set_xlabel('Coordinate');
ax[0].set_ylabel('Counts');

decisionboundary = np.linspace(-4,4,100) # Move the decision boundary around
truepositives = np.sum([distA > i for i in decisionboundary], axis=1)
truenegatives = np.sum([distB < i for i in decisionboundary], axis=1)
falsepositives = np.sum([distB > i for i in decisionboundary], axis=1)
falsenegatives = np.sum([distA < i for i in decisionboundary], axis=1)

falsepreds = (falsepositives + falsenegatives)

ax[1].plot(decisionboundary,falsepreds, label='Total false \n preds');
ax[1].axvline(x=decisionboundary[np.argmin(falsepreds)], lw=2, color='r', alpha=0.5);
ax[0].axvline(x=decisionboundary[np.argmin(falsepreds)], lw=2, color='r', alpha=0.5);
ax[1].set_xlabel('Coordinate');
ax[1].legend()
plt.show()
```


<img src="/assets/images/MBC_11_0.png" width="500" alt="me" align="center" hspace="40" vspace="40">



#### 2.2 Precision


```python
fig, ax = plt.subplots(1,2, figsize=(10,3))
ax[0].hist(distA, bins=100, alpha=0.5);
ax[0].hist(distB, bins=100, alpha=0.5);
ax[0].set_xlabel('Coordinate');
ax[0].set_ylabel('Counts');

precision = truepositives/(truepositives+falsepositives)

ax[1].plot(decisionboundary,precision, label='Precision');
ax[1].set_xlabel('Coordinate');
ax[0].axvline(x=3, lw=2, color='b', alpha=0.5, label='P = 1');
ax[1].axvline(x=3, lw=2, color='b', alpha=0.5, label='P = 1');
ax[0].legend() ;
ax[1].legend() ;
plt.show()
```


<img src="/assets/images/MBC_13_0.png" width="500" alt="me" align="center" hspace="40" vspace="40">



Minimising the overall error is not necessarily an optimal aim for all situations, however. In an application like email spam detection, for example, if an important email was inadvertently marked as spam, this would be more costly than if a few spam emails got through the filter. We can thus choose a metric which allows us to avoid false positives. The metric which does this determines how many positive predictions are actually correct, and it's known as 'precision':

\begin{equation}
\text{precision}=\frac{TP}{TP + FP} 
\end{equation}

A model which has no false positives at all has a perfect precision value of 1.0. False positives are represented on the graph by the part of the orange distribution which lies to the right of our decision boundary. So, by positioning the decision boundary far to the right, you will ensure that the entire orange distribution lies to the left of the boundary, and so you wil have no false positives at all, and perfect precision.

Of course, moving the boundary further to the right results in a decreasing number of true positives; eventually the model predicts everything as false. Such a model is not useful for classification, but will still have perfect precision. So we can see that the precision metric is not sufficient by itself. 

#### 2.3 Recall


```python
fig, ax = plt.subplots(1,2, figsize=(10,3))
ax[0].hist(distA, bins=100, alpha=0.5);
ax[0].hist(distB, bins=100, alpha=0.5);
ax[0].set_xlabel('Coordinate');
ax[0].set_ylabel('Counts');

recall = truepositives/(truepositives+falsenegatives)

ax[1].plot(decisionboundary,recall, label='Recall');
ax[1].set_xlabel('Coordinate');
ax[0].axvline(x=-2.5, lw=2, color='g', alpha=0.5, label='R = 1');
ax[1].axvline(x=-2.5, lw=2, color='g', alpha=0.5, label='R = 1');
ax[0].legend(); 
ax[1].legend();
plt.show()
```


<img src="/assets/images/MBC_16_0.png" width="500" alt="me" align="center" hspace="40" vspace="40">



The converse of precision is a metric known as 'recall', defined as 

\begin{equation}
\text{precision}=\frac{TP}{TP + FN} 
\end{equation}

This metric determines the fraction of all true events (the blue distribution) correctly predicted, and thus it favours avoiding false negatives. A situation where false negatives may be dangerous is a medical test, where incorrectly thinking you have a dangerous disease may be less of a worry than incorrectly thinking you don't have it. 

Recall behaves in the opposite way to precision: positioning the decision boundary to the left ensures the entire blue distribution is on the right-hand side of the boundary, giving a perfect recall score of 1.0. Again, the metric is insensitive to how many true negatives you have: if the boundary goes far to the left, and your model always outputs a 'true' prediction, you will have perfect recall, but also no true negatives. 

#### 2.4 Balancing precision and recall: the F1 metric

While both precision (which favours no false positives) and recall (which favours no false negatives) are sometimes useful metrics in their own right, you can see that a more optimal situation might arise when precision and recall are balanced. The geometric mean of the two quantities is known as the F1 metric:

\begin{equation}
\frac{1}{F1}=\frac{1}{2}\left(\frac{1}{\text{Precision}}+\frac{1}{\text{Recall}}\right)
\end{equation}

A maximal F1 score results from both high precision and recall values. It results in a boundary location which is similar to (but not necessarily the same as) the minimium error location.


```python
fig, ax = plt.subplots(1,2, figsize=(10,3))
ax[0].hist(distA, bins=100, alpha=0.5);
ax[0].hist(distB, bins=100, alpha=0.5);
ax[0].set_xlabel('Coordinate');
ax[0].set_ylabel('Counts');

F1 = 2/(1/precision+1/recall)

ax[1].plot(decisionboundary,F1, label='F1');
ax[1].set_xlabel('Coordinate');
ax[0].axvline(x=decisionboundary[np.argmax(F1)], lw=2, color='m', alpha=0.5, label='Max F1');
ax[1].axvline(x=decisionboundary[np.argmax(F1)], lw=2, color='m', alpha=0.5, label='Max F1');
ax[0].legend(); 
ax[1].legend();
plt.show()
```
<img src="/assets/images/MBC_20_0.png" width="500" alt="me" align="center" hspace="40" vspace="40">



#### 2.5 The ROC curve

A final metric we will look at is a plot of the normalised true positives against false positives. To obtain this curve, we slide the decision boundary from left to right.

At far left values, the model predicts all true positives correctly; it also predicts the maximum number of false positives, as the entire orange distribution is located to the right of the boundary. As the boundary slides rightwards, the number of false positives decreases as the boundary slides over the orange distribution; the number of true positives is still at maximum.

At some point, the boundary will start sliding over the blue distribution. As this happens, the number of true positives decreases, until eventually the boundary is entirely to the right of all the data. Then, both the number of false positives as well as true positives is zero.

The curve traced out from the point (1,1) to (0,0) is known as the receiver operating characteristic, or ROC curve. As you can see in the following examples, the ROC curve depends on the shape of the data distribution (in general it is also a function of the model). If the data is on top of each other, no decision boundary will be able to discriminate between true and false values; the ROC curve is a straight y=x line. If the data is cleanly separated, the curve forms a sharp right angle.

A more optimal classifier has an ROC curve closer to the right angle. It turns out that the corresponding metric which is maximised is the area under the curve, or AUC. 


```python
np.random.seed(42)
nsamplestrue  = 2500
nsamplesfalse = 10000
mean = 0
distA = np.random.randn(nsamplestrue)+mean
distB = np.random.randn(nsamplesfalse)-mean
decisionboundary = np.linspace(-6,6,100) # Move the decision boundary around
truepositives = np.sum([distA > i for i in decisionboundary], axis=1)
truenegatives = np.sum([distB < i for i in decisionboundary], axis=1)
falsepositives = np.sum([distB > i for i in decisionboundary], axis=1)
falsenegatives = np.sum([distA < i for i in decisionboundary], axis=1)

fig, ax = plt.subplots(3,2, figsize=(10,9))
ax[0,0].hist(distA, bins=100, alpha=0.5);
ax[0,0].hist(distB, bins=100, alpha=0.5);
ax[0,0].set_ylabel('Counts');

ax[0,1].plot(falsepositives/nsamplesfalse, truepositives/nsamplestrue, label='ROC');   
ax[0,1].set_ylabel('True positive rate'); 
ax[0,1].legend() ; 

mean = 1
distA = np.random.randn(nsamplestrue)+mean
distB = np.random.randn(nsamplesfalse)-mean
decisionboundary = np.linspace(-6,6,100) # Move the decision boundary around
truepositives = np.sum([distA > i for i in decisionboundary], axis=1)
truenegatives = np.sum([distB < i for i in decisionboundary], axis=1)
falsepositives = np.sum([distB > i for i in decisionboundary], axis=1)
falsenegatives = np.sum([distA < i for i in decisionboundary], axis=1)

ax[1,0].hist(distA, bins=100, alpha=0.5);
ax[1,0].hist(distB, bins=100, alpha=0.5);
ax[1,0].set_ylabel('Counts');

ax[1,1].plot(falsepositives/nsamplesfalse, truepositives/nsamplestrue, label='ROC');  
ax[1,1].set_ylabel('True positive rate'); 
ax[1,1].legend(); 

mean = 3
distA = np.random.randn(nsamplestrue)+mean
distB = np.random.randn(nsamplesfalse)-mean
decisionboundary = np.linspace(-6,6,100) # Move the decision boundary around
truepositives = np.sum([distA > i for i in decisionboundary], axis=1)
truenegatives = np.sum([distB < i for i in decisionboundary], axis=1)
falsepositives = np.sum([distB > i for i in decisionboundary], axis=1)
falsenegatives = np.sum([distA < i for i in decisionboundary], axis=1)

ax[2,0].hist(distA, bins=100, alpha=0.5);
ax[2,0].hist(distB, bins=100, alpha=0.5);
ax[2,0].set_xlabel('Coordinate');
ax[2,0].set_ylabel('Counts');

ax[2,1].plot(falsepositives/nsamplesfalse, truepositives/nsamplestrue, label='ROC');  
ax[2,1].set_xlabel('False positive rate');  
ax[2,1].set_ylabel('True positive rate'); 
ax[2,1].legend(); 

plt.show()
```
<img src="/assets/images/MBC_23_0.png" width="500" alt="me" align="center" hspace="40" vspace="40">

---

### 3. Conclusions

conclusions

A jupyter notebook is available for this tutorial on my [GitHub repository](https://github.com/nadanai263/datasciportfolio).
