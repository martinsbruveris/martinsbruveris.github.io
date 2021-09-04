---
layout: post
title: What is Mathematics?
date: 2017-08-05T14:00:22+00:00
author: Martins Bruveris
tags: mathematics expository
---
A couple of weeks ago I was asked to help out with the upcoming induction week for our mathematics undergraduate students. I am given one hour to give 70 students some idea of what they have come to study. After thinking about the question, "What is mathematics?", I have found the following four-fold answer.

Mathematics helps us to...
- ... find precise answers to precise questions.
- ... find approximate answers to vague questions.
- ... interpret answers that seem precise but are not.
- ... figure out the right questions to ask.

Below I have tried to develop these points and to supplement them with examples. The text below is addressed at first year students of mathematics at Brunel University.

<!--more-->

### 1. Finding precise answers to precise questions.

This is what one usually associates with mathematics and it is true that mathematics provides tools that can be used to find precise answers to sufficiently precisely formulated questions.

For example, a question that might be of interest to you is, how much money will you owe to the Student Loans Company by the time you graduate? Assuming average earnings, how long will it take you to pay back the loans and what will be the total amount you paid for the university education? Mathematically, the answer requires little more than an understanding of compound interest and geometric series. One also has to read the rules for student loans: how are interest and repayment rates calculated based on the salary. You will explore this question in more detail in group projects this year.

More complicated questions that never the less require precise answers arise in space flight. How do we navigate a spacecraft to a comet flying through space and land a robot onto its surface? In 2016, the <a href="http://www.esa.int/Our_Activities/Space_Science/Rosetta">Rosetta mission</a> successfully landed a spacecraft on a comet 4 km in diameter that is 780 million kilometers away. The necessary precision could be compared to throwing a baseball from London to Tehran and hitting a particular window. As many comparisons go, this one is <a href="https://what-if.xkcd.com/82/">not entirely accurate</a>.

Planets, comets and spacecraft move according to Newton's law of gravity: Two objects attract each other with a force that is proportional to the product of their masses and decreases with the square of the distance. From this one can derive the equations governing the motion of the sun, earth, moon and other celestial objects. If we can compute sufficiently precise solutions then we can plan the trajectory of our spacecraft. The recent book and film <a href="https://en.wikipedia.org/wiki/Hidden_Figures_(book)">Hidden Figures</a> is in part about solving these equations before computers became available.

### 2. Finding approximate answers to vague questions.

Consider the question:

<p style="text-align:center;">How many bricks are there in London?</p>

This question is different in nature. We don't want to know the exact number. In fact, no one knows exactly how many bricks there are in London. The actual answer is unknown and unknowable. But we can estimate, how big the answer is, how many zeros the number contains. Mathematical reasoning provides a tool to arrive at this rough approximation. Mathematics is not always about precision in the sense of precise numbers. Mathematics is about precise thinking, even when the numbers themselves are imprecise. Using approximations is fine as long as we are aware of what we are doing.

The technique used to approach questions such as this is called <a href="https://en.wikipedia.org/wiki/Fermi_problem">Fermi estimation</a>. We can arrive at an answer by estimating how many bricks are necessary to build houses for all inhabitants of London to live in.

- There are about 10 million people living in London
- The average household size in the UK is 2.4 people, meaning that there are about 4 million households in London. We are not too bothered with precise numbers here. Since our estimates are only approximate, rounding them will not do much harm either.
- An average two bedroom flat has about 80 square meters. Let us assume our flats are square, divided into four equally sized rooms. The total length of walls of such a flat would be $$6\sqrt{80}$$ meters. If we replace 80 by 81, this becomes 54 meters of walls.
- How high are ceilings? Let us say, they are 2.5m high. This is generous as was the 80 square meter estimate, but we need to compensate for the fact that we only take residential buildings into account. This would give around $$50 \cdot 2.5 = 125 \text{m}^2$$ square meters of wall per household.
- One brick is $$20\text{cm} \times 5\text{cm}$$, meaning it has an area of $$100\text{cm}^2$$. Assuming two rows of bricks per wall this comes to 200 bricks per square meter of wall.
- Multiplying 4 million households with $$125\text{m}^2$$ of wall with 200 bricks gives us 100 billion bricks in all of London.

We have arrived at an approximate answer. Does this answer make sense? At first glance it does. The number is big but not too big. We can try to check our answer by estimating the number of bricks in a different way.

- According to <a href="https://www.statista.com/statistics/472894/annual-brick-production-great-britain/">Statista</a> the following number of bricks (in billions) was produced in the UK in the last couple of years.

  | 2012  | 2013  | 2014  | 2015  |
  |-------|-------|-------|-------|
  | 1.459 | 1.555 | 1.824 | 1.919 |

- London has about 1/8th of the population of the UK, so let us assume that 1/8th of all bricks was used in London. If, on average, 1.6 billion bricks were produced in a year, then 200 million bricks were used in London each year.
- Assume that houses are up to 100 years old and that brick production has not changed in the last century. Then London would contain 20 billion bricks.

We see that we have arrived at a different answer, but within the same order of magnitude. For our purposes we call this a success.

### 3. Understanding seemingly precise answers to vague questions.
A weather forecast predicts not only temperature, wind speed and direction, but also the chance that it will rain at a given time in a given place. The statement

<p style="text-align:center;">"There is a 60% chance that it will rain in Uxbridge tomorrow between 1-2pm."</p>

seems clear at first glance, but what is really meant by it? A couple of possible interpretations come to mind.

1. If 100 people are in Uxbridge during that time, 60 will get wet.
2. During the hour there will be 24 minutes of sunshine and 36 minutes of rain.
3. The number 60% is what the weather forecasting model "believes" about the future.

In the context of a weather forecast for Uxbridge and a time interval of one hour, answers (1) and (2) sound absurd. They make more sense if the forecast is meant for the next 24 hours and all of South England. Nowadays, however, we have smartphones with GPS receivers so the weather app knows exactly where we are and can provide a supposedly personalised forecast.

What are we to make of answer (3)? This answer is at the same time more honest and less helpful. Really understanding what it means would require one to look at the details of the model, to inspect the equations that are used to calculate the chance and to consider the model's past predictions and how they compared with reality. Doing all this requires mathematics. See the post on <a href="https://mathbabe.org/2015/12/28/forecasting-precipitation/">mathbabe</a> for suggestions how to measure the accuracy of such a forecast.

### 4. Figuring out the right questions to ask.

> To ask the right question is harder than to answer it. —Georg Cantor

Sometimes we need mathematics to start asking the right questions. Consider the problem of predicting stock prices. If we <a href="https://markets.ft.com/data/equities/tearsheet/summary?s=IBM:NYQ">plot</a> the daily price of a stock over a time span of several years, the result looks erratic. There is a lot of up and down, sometimes more up than down, sometimes more down than up. There is noise in the short-term and perhaps some discernible trend in the long-term. Sometimes there are sudden movements while at other times the stock price makes an excursion only to return after some time to where it started.

How do we make sense of this? The question "What will be the stock price tomorrow?" is not very helpful. The price of a stock is an estimate of the value of a company which is affected by the performance of the company, current events and public opinion. To make accurate prediction a crystal ball is indispensable. We have more hope answering other questions: Can we detect trends? Can we identify clusters of similarly behaving stocks? Can we distinguish between "risky" and "safe" stocks? What do these terms even mean? For this mathematicians working in universities, banks, hedge funds and financial companies have developed tools: these have names you have heard like average, mean, variation, correlation and more complicated ones like volatility, regression, multi-factor model. You will see some of these in the modules on probability theory and statistics and others in more specialized modules on financial mathematics.

A different question, one where the role of mathematics is less obvious, is the ranking of search results. If you enter a search term such as "running" into <a href="https://www.google.co.uk/search?q=running">Google</a>, you are presented with a list of pages that have to do with running. Imagine yourself having to design the algorithm in the background: How do you choose which page to put at the top? As humans we make intuitive judgements about importance and relevance every day. Given twenty links to websites about running, we would be able to sort them according to what we perceive as relevance. Of course, if 50 of us did this exercise, we would end up with 50 different rankings. For the whole internet this approach is impractical. It is worth remembering that about 20 years ago, however, there were websites dedicated to providing curated web directories—lists of links to web pages sorted by categories, like a books in a library. Yahoo <a href="https://en.wikipedia.org/wiki/History_of_Yahoo!">started out</a> as such a website. These directories were useful, because no one had figured out yet how to search and sort the internet automatically. Search engines did exist, but their results were not always very helpful.

Google's rise to become one of the dominant internet companies began with search. More precisely, with an algorithm to automatically determine the relevance of web pages. The algorithm, called PageRank, recasts the problem in the language of graphs or networks. Each web page is represented by a node in the network and we create a link from one node to another, if there is a hyperlink between the two web pages. Then we imagine placing a bucket of water at each node and letting it flow through the links of the network. Nodes with no or few incoming links will receive no or little water, while its initial water will flow away through any outgoing links; these nodes are the unimportant ones. On the other hand, water will accumulate at nodes with many incoming links; these will be the important nodes in the network. By observing the amount of water in each node over time we can get a sense of what the network thinks about the importance of each node. Mathematically this can be expressed using the language of linear algebra. You will explore the PageRank algorithm in more detail in projects later in the year.

We have seen how PageRank recasts the problem of ranking search results in the language of mathematics. While doing so, we must not forget that the problem we started with has no unique solution. There is no "right" or "wrong" order of ranking web pages. If I search for web pages about "running" I might be looking for running clubs, running magazines, tips how to start running, running shoes or pictures of people running. Mathematics can help us think about the problem more clearly, see what is important and gain insight into the problem, but it does not answer the question "Which web page is most relevant for running?"
