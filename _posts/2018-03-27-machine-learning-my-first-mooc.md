---
layout: post
title: Machine Learning – My First MOOC
date: 2018-03-27T19:11:16+00:00
author: Martins Bruveris
tags: machine-learning
---
I have been engaged with higher education for the past 15 years, starting first as an undergraduate and now as lecturer teaching undergraduates myself. The rise of MOOCs, from 2012 onwards happened after I had finished my graduate studies and from then on I learned new material mostly by reading textbooks, research papers and spoke directly to colleagues. Although I was aware of their popularity, MOOCs never seemed important enough to warrant a closer look. Until now that is.

<!--more-->

Two things have changed: First, professionally MOOCs are “the competition”, they provide higher education at a fraction of the cost of a university. This is particularly true in England, where the cost of a university degree is £9,000 per year and rising. Even a distance learning institution such as the Open University is significantly more expensive than most MOOCs. So I wanted to experience how a MOOC feels like for learners. To see how they use technology, how they pace videos and design programming exercises and what we as lecturers can learn from them. Second, academics in the UK were striking for 14 days to avoid steep cuts to their pensions and hence I found myself with spare time on my hands.

Thus I decided to dive into the MOOC experience. After some research, I chose the grandfather of MOOCs, Andrew Ng’s Coursera course on <a href="https://www.coursera.org/learn/machine-learning">machine learning</a>.

### Overall structure

The course lasts 11 weeks and each week usually covers one specific algorithm. The course starts with linear regression, continues with logistic regression and neural networks, covers support vector machines, K-means and PCA and finally talks about optimizing large scale problems via batch and stochastic gradient descent. Each week has about 1.5 to 2 hours of video content of Andrew Ng explaining the mathematics behind the algorithm of the week. Then there is a multiple choice test and finally a programming exercise that is submitted and automatically graded online.

### Observations

- Andrew Ng succeeds in presenting a large amount of mathematics with a minimum of mathematical prerequisites. As mathematicians we tend to think that to understand a topic we have to understand it down to the last detail. It is ingrained in how we were taught. In the mathematics curriculum analysis and the epsilon-delta notion of convergence is seen as the bedrock upon which calculus is built. In this course however Andrew Ng explains and uses the gradient algorithm without assuming any knowledge of calculus! Seeing how this is done is fascinating.

- Each lecture is followed by a multiple choice quiz which tests understanding of the material. I really liked the questions. They take the material of the lectures and then go just a little bit further to check whether we actually thought about what Andrew Ng said in the video.

- In the programming exercises we are implementing all the basic machine learning algorithms in Matlab (or Octave) from scratch: linear and logistic regression, k-means clustering, neural networks. For this to work a lot of scaffolding is provided by the course. One can argue that creating the scaffolding—helper functions to read in data, visualise the results, etc.—is at least as challenging as coding the core of the algorithm. But of course too much freedom makes it impossible to automatically grade the programming exercises.

### What have I learned?

- Machine learning. I had seen some of the algorithms before, but seeing a systematic exposition of the material was helpful. However, particularly useful were the nuggets of wisdom on the practical aspects: how to divide a dataset into training and test sets, how to go about optimising learning rates and other hyper-parameters, how to diagnose bias and variance. All the things that are needed to make theory work.

- Focus. Everything in the course is focused towards a goal. The course starts with basics: cost functions, nonlinear optimisation and gradient descent, but everything is introduced only to the extent that it is strictly necessary: maximum likelihood is mentioned only once in passing, and is convexity; conjugate gradient method is used in programming exercises but only as an optimisation black box. In a 50 minute lecture there is the temptation to wander and explore side avenues because they are interesting or because they provide “a more complete picture”, but if the lecture is split into 10 minute long videos, focus becomes essential.

All in all it was both an enjoyable and instructive experience. I am looking forward to continuing with the spiritual successor, Andrew Ng’s second MOOC on deep learning.
