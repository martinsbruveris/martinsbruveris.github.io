---
layout: post
title: Christmas Lecture
date: 2017-12-14T19:28:59+00:00
author: Martins Bruveris
tags: mathematics
---
For the Christmas lecture of my multivariable calculus module I tried find something entertaining to present. This turned out to be quite difficult, but in the process I across this multivariable calculus themed comic.

<!--more-->

<a href="https://warandpeas.com/2016/10/20/relevant/"><img src="{{ '/assets/images/2017/12/warandpeas_relevant.jpg' | relative_url }}" alt="War and Peas - Relevant" width="451" height="500" /></a>

In the end I settled on showing how to evaluate
\\[ \zeta(2) = \sum_{n=1}^\infty \frac 1{n^2} = \frac{\pi^2}6\,. \\]

There are many proofs for this identity---fourteen have been been <a href="https://empslocal.ex.ac.uk/people/staff/rjchapma/etc/zeta2.pdf">collected</a> by <a href="https://empslocal.ex.ac.uk/people/staff/rjchapma/rjc.html">Robin Chapman</a>—and it is often done as an application of the theory of Fourier series. One of the proofs, however, uses double integrals: using the geometric series one can show that
\\[ \sum_{n=1}^\infty \frac 1{n^2} = \int_0^1 \int_0^1 \frac{1}{1-xy} \,\mathrm{d}x \,\mathrm{d}y\,. \\] Evaluating this integral is an instructive exercise because it confounds many of the assumptions about double integrals students may have: the integration domain is as simple as one might hope, yet the right approach is to perform a change of coordinates; the resulting domain can be parametrized as one piece, yet it is better to split the domain in two; non-trivial trigonometric identities are used to simplify the integrands.

Whether or not the students were entertained remains unknown, but as a result I wrote some notes explaining the calculation in great, perhaps excessive, detail.

<iframe src="https://drive.google.com/file/d/1B7McMq9njJ6Gl2Oq9KNJGkJ8NssHD1WJ/preview?usp=drivesdk" width="100%" height="600px"></iframe>
