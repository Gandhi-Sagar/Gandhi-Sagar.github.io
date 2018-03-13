---
title: 'Machine Learning 1 : Introduction - What and How'
author: Sagar Gandhi
layout: post
permalink: /Machine-Learning-1-Introduction_what_and_how
categories:
  - Posts
---

<h3>Hello</h3>

A warm welcome to this very first part of series of Machine Learning blog. 
In this part, it will be defined

+ Why this Machine Learning blog series?
+ What is it?
+ Who is this for?
+ Prerequisites
+ Getting Started

<br>
**Why**
  
<!--excerpt.start-->
Let's get started. While working with people from different backgrounds, I came to an understanding that, overall, there are many misconceptions about Machine Learning.  
  
One set of people think of Machine Learning as an area full of Mathematical jargon. On top of this thinking they either come close or keep a safe distance.
Another group thinks this is highly advanced stuff, and they will not be able to pick it up. Therefore, they are too afraid to approach a problem
Few people think of it as a magic box, which will chew in any data and produce some unrealistic results.
From my experience, I also think that people who say 'I know how to do it' actually miss few fundamental things.  
  
I think there is a need to think of this domain without having any biases, as well as understand and accept the fundamental rules.  
  
<!--excerpt.end-->  <a class="anchor" id="read-more"></a>

**What**
  
I have decided to put up a series. It would be a step-by-step guide explaining the basics. Each newly introduced concept will give focus on how to solve problems using Machine Learning. Each concept will be introduced using an example. Each example will be more or less a real-life scenario.
Minimal mathematics will be covered. At a certain stage it cannot be avoided, but readers from non-mathematical background will find it easy to follow.
  
I am optimistic about posts being interactive. Whenever you feel like responding, do not hesitate, your perspectives, a constructive criticism will be  welcomed.
  
[Vivek Kulkarni](https://www.linkedin.com/in/vivekkulkarni) is going to review of the content and also help me in formalizing the content.
  
**Who this is for**  
  
Simply put, it will be targeted towards people who are interested in Machine Learning.
But to further categorize it:
  
+ For a newbie, it will serve as a getting started guide. It will effectively point out how one should approach a problem. It is advised that this type of reader should write the code.
+ For an experienced fellow, this series will serve a role of 'What-to-do' and 'Steps-involved', so that this type of user can formulate use-cases effectively. It is also advised that reader of this type should also write the code, but provided enough experience of understanding the things on the fly, one can choose.
  
People who actually want to build beautiful products, products which have impact on real life, will understand how to use Machine Learning as a tool.
  
**Prerequisites**
  
All the examples will use Python to demonstrate the examples. Therefore, it is expected that you have a familiarity with Python.  
  
As we will be using Python, and as we all know Python is interpreted language, it is slower for numerically heavy operations (well, than C or Fortran). We will be dealing with multidimensional arrays, so if we rely on only Python, it would take ages to be able to conclude anything useful.
Hence, following things will be used:  

+ NumPy: NumPy offers highly optimized multidimensional arrays. The way it works is Python offloads number crunching to lower level extensions written in C or Fortran.
+ SciPy: SciPy offers many numerical recipes around NumPy's arrays. If you are simply reading this, and not understanding what I am talking about, it is recommended that you read this FAQ
+ MatPlotLib: This will be our tool for plotting and visualizing high quality graphs.
  
**Getting Started**
  
Okay, we are all set to get started.  
  
What is Machine Learning, in layman's terms? Well, as you may already know, It is process of teaching Machines(Software) to carry out tasks.  
e.g. Sorting the emails as per the category.  
  
How can it be done? In general, we will have some data, we will process it, and will try some cool algorithm. If not satisfied, we will continue with the next iteration of the same cycle.  
  
If you have heard some buzz-words like SVM or KNN, these words themselves are not Machine Learning. Rather they are tiny fraction of ML. In general, more time has to be spent on mundane tasks such as:  
  
1. Data gathering, cleansing and understanding it.
2. Choosing the right algorithm.
3. Expose our data to chosen algorithm.
4. "Correctly" measure the performance.
  
This seems good enough to start with. We just saw a tiny introduction of Machine Leaning.  
  
In the next post, we will answer the fundamental question - "Why does it matter to me?".This question will not only serve as a motivation, but also give a notion of where Machine Learning is used.  
  
Note: A special thanks to [Vivek](https://www.linkedin.com/in/vivekkulkarni), he wholeheartedly reviewed this post and provided valuable suggestions.


