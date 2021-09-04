---
layout: post
title: Representation Learning with Contrastive Predictive Coding
date: 2021-07-18 12:00:00
author: Martins Bruveris
tags: deep-learning paper-review
---

Contrastive Predictive Coding is an unsupervised learning approach for learning useful
representations from high-dimensional data.

<!--more-->

Given sequence data $$x_1, \dots, x_t, \dots$$, we want to learn a latent
representation $$c_t$$, called *context*, that allows us to predict future observations
$$x_{t+k}$$. The context may depend on all previous sequence elements,
$$c_t = g(x_{\leq t})$$.

One approach would be to use an auto-regressive encoder to generate the context, $$c_t =
g(x_{\leq t})$$, and a generator to generate a prediction $$\tilde x_{t+k} = h(c_t)$$ for
the true value $$x_{t+k}$$, combined with a loss $$\mathcal L(\tilde x_{t+k}, x_{t+k})$$
measuring the prediction error. This approach faces two challenges: (1) Defining a good
loss for high-dimensional data $$x_{t+k}$$ is not easy; simple losses like mean
squared-error focus too much on details that are essentially noise and too little on
global structure. (2) The generative model $$h(c_t)$$ for faithfully recreating
high-dimensional data, e.g., high resolution images, is computationally expensive.

What does this paper propose to do instead? Instead of predicting the future in the input
space $$x$$, the authors propose to use a lower-dimensional *latent representation* $$z$$.
In more detail, we train an *encoder* $$z_t = g_{\mathrm{enc}}(x_t)$$ which maps the input
signal $$x_t$$ at each time-step to a latent representation $$z_t$$; we also train an
*autoregressive* model $$c_t = g_{\mathrm{ar}}(z_{\geq t})$$ which summarises the latent
information across time-steps into a context $$c_t$$. The context will be used to
predict the future signal, but the prediction will take place in latent space.

We are free to choose (read: train) any model to make this prediction, but because
predicting the future is essentially only an auxiliary task to learn useful latent and
context representations, in this paper the prediction is a simple (learned) linear
projection $$\tilde z_{t+k} = W_k c_t$$, with a different $$W_k$$ for each prediction step
size.

Finally, what loss should be use to score the quality of predictions? The authors opt for
a version of *noise contrastive estimation (NCE)*. They select a set of $$N$$ samples
$$X=\{x^1, \dots, x^N \}$$, where one sample, say, $$x^1$$ is the actual sequence element,
$$x_{t+k}$$, while the other $$N-1$$ samples are randomly chosen from the data. This part
about "randomly chosen" was a bit unclear to me, but it would appear that they select
other sequence elements *from the same sequence* as negative sample. In the probabilistic
language of NCE, we choose one *positive* sample from the distribution $$p(x_{t+k}|c_t)$$
and $$N-1$$ *negative* samples from the distribution $$p(x_{t+k})$$. The loss function is
a softmax loss

$$
\mathcal L = -\log \frac {f_k(x_{t+k}, c_t)}{\sum_{j} f_k(x^j, c_t)}\,.
$$

to classify, which prediction is the correct one; here, $$f_k(x_{t+k}, c_t) =
\exp(z_{t_k}^T W_k c_t)$$. They call their loss function *InfoNCE*.

Why not use a simpler loss, like mean squared error instead? The authors don't discuss
this, I suspect that the reason is as follows: The latent representation has no intrinsic
meaning. A loss like mean square error assumes that each dimension of the latent
representation is meaningful and all dimensions are equally meaningful. This does not have
to be the case. We don't need to know how close our prediction is to the true sequence
element in absolute terms, we only need to know that it is closer to the true element than
to latent representations of all other data points.

And this is where NCE comes in. The concept of "all other data points" is computationally
intractable, just like in NLP it is intractable to compare a word prediction against all
other words in the dictionary. So, instead we choose a sample of $$N-1$$ negative data
points and compare against them. This is computationally efficient and works in practice.

An interesting idea from David McAllester's
[video](https://www.youtube.com/watch?v=zNKMHj1eLa0): What is the difference between
signal and noise? Signal is the part that is useful for predicting the future and noise is
all the rest.
