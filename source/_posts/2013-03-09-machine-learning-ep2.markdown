---
layout: post
title: "Machine learning ep2"
date: 2013-03-09 22:56
comments: true
categories: Algorithm
math: true
tags: [MachineLearning]
---



* [Linear Regression](http://en.wikipedia.org/wiki/Linear_regression)
--------

It's a simple model,and very useful


* Locally Weighted Linear Regression
-------

the training data only contain the data near X  (is not it cool?)

<!-- more -->

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


* ####Discriminative learning algorithm

learn 
$$
P(y|x)
$$
or

$$ 
h_{\theta}(x) \in \left \{ 0,1 \right \}
$$

directly



* ####Generative learning algorithm

learn
P(x|y)

then
P(y)

such as Native Bayes

* ####Laplace smoothing

A/B

==>

(A+1)/(B+1)





