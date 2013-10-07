---
layout: post
title: "Machine Learning ep1"
date: 2013-02-16 22:35
comments: true
categories: MachineLearning
---


* The general steps of Machine Learning
------------

#### 1, Traning Data & Test Data ####

This step is the most important part in machine learning. 
Very easy to ignore and underestimate.

Data is wrong, all the steps afterward are wrong.

More than that, it's very difficult to understand where is wrong, how to impove that.

Maybe the data can be produced by online logs by user.

Maybe the data can not be gotten from machine, only by humans.

#### 2, [Feature Selection](http://en.wikipedia.org/wiki/Feature_selection)  ####

The less feature, the faster the model is calculated and updated

The more feature's meaning is ,the less prabability overfit happens 

L1-norm

http://www.cnblogs.com/heaad/archive/2011/01/02/1924088.html

#### 3, Model Choosing ####

Different model has its personality

#### 4, Evaluation ####

RSE

FRTP...

--------------------------------------------

* [Linear Regression](http://en.wikipedia.org/wiki/Linear_regression)
--------

It's a simple model,and very useful


* Locally Weighted Linear Regression
-------

the training data only contain the data near X  (is not it cool?)

------------------------------

* Batch Gradient Descent vs stochastic gradient descent（随机梯度下降）
---------

#####Batch Gradient Descent: #####

> You need to run over every training example before doing an update, which means that if you have a large dataset, you might spend much time on getting something that works.

#####Stochastic gradient descent: #####
> on the other hand, does updates every time it finds a training example, however, since it only uses one update, it may never converge, although you can still be pretty close to the minimum.

In conclusion, if your data is large, you might want to use Stochastic Gradient Descent.

####Newton Method: ######
Advantage: fast iteration to get result
Disadvantage: have to invert the N*N's (N is the number of feature) every step


* When to stop the iteration of Gradient Descent
--------
It has two ways.

`Condition 1: the two iteration's result is so close to ignore`

`Condition 2: the loss is very small, which can be ignored`

* parametric learning algorithm & non-parametric learning algorithm

> non-parametric learning: the number of arguments will go with size of training data

* Exponential distribution family
-------
eg: Normal Distribution, Poisson Distribution, Bernouli Distribution 


* Logistic Regression & Softmax Regression
------
Logistic Regression only 2 kinds
Softmax Regession can solve multi

----------------------------

Reference:
----------

[http://zhfuzh.blog.163.com/blog/static/14553938720127309539465/]()

[http://blog.csdn.net/pennyliang/article/details/6998517]()

[http://deeplearning.stanford.edu/wiki/index.php/UFLDL%E6%95%99%E7%A8%8B]()

It's not finished...
