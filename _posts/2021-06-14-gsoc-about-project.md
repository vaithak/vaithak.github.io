---
layout: single
title:  "GSoC: About the project"
date:   2021-06-14
tag: gsoc
toc: true
toc_sticky: true
comments: true
header:
  teaser: /assets/images/gsoc.png
---

I have been selected as a student developer in [Google Summer of Code](https://summerofcode.withgoogle.com/) under the [GeomScale organization](https://geomscale.github.io/) & will be spending the upcoming 10 Weeks working on the project of "Counting Linear Extensions". This post will be for providing details regarding my project, mainly problem formulation and proposed solution.

<center>
    <img src="/assets/images/gsoc.png" width="40%" height="200" /> + <img src="/assets/images/geomscale.png" width="40%" height="200" />
</center>
[Project link](https://summerofcode.withgoogle.com/projects/#6649856422051840)  

## About the Organization
From the webpage of the organization:  
> "GeomScale is a research and development project that delivers open source code for state-of-the-art algorithms at the intersection of data science, optimization, geometric, and statistical computing".

I have always been interested in the field of applied maths and this project immediately caught my attention.  
Currently, the organization works on mainly two open-source libraries, one of which is [volesti](https://github.com/GeomScale/volume_approximation), which is a C++ library with R and python interfaces, this library contains several algorithms for high-dimensional sampling and volume approximation. The other one is the recently introduced [dingo](https://github.com/GeomScale/dingo), which is a python package for analyzing metabolic networks.

## Introduction to my project
`It is possible that you may not be familiar with many terms in this section, don't worry, I will try to explain them in detail in the upcoming section.`

In brief, my project is to extend the functionality of the `volesti` library, specifically implementing the functionality of "Counting Linear Extensions of a poset (partially ordered set)".  
This problem can be converted to computing volume of a convex polytope, for which `volesti` contains state-of-the-art algorithms.  

Exact problem description is as following:  
> The problem of counting the linear extensions of a given partial order consists of finding (counting) all the possible ways that we can extend the partial order to a total order that preserves the original partial order.  
It is an important problem with various applications in Artificial Intelligence and Machine Learning, for example in partial-order plans and in learning graphical models. It is a well known #P-complete problem; the fastest known algorithms are based on dynamic programming and require exponential time and space.  
Nevertheless, if we only need an approximate counting attained with a probability of success, then the problem admits a polynomial-time solution for all posets using Markov chain Monte Carlo (MCMC) methods.   
This project aims to implement new randomized methods to approximate the number of linear extensions based on volume approximation.

## Mathematical details of the project
First, I will start by introducing the terms used in the above section, because one all the terms are clear, the aim of the project will become crystal clear (hopefully).

### Poset
The term “poset” is short for “partially ordered set”, that is, a set whose elements are ordered but not all pairs of elements are required to be comparable in the order.   

I will try to explain with an example:  
Let's say you have marks of `n=5` students in two exams, the marks of a student (out of 10) 
is given as a pair, let the marks of the students be as follows:  

| student-id   | marks    |
|--------------|----------|
| $S_1$           | (5, 8)   |
| $S_2$           | (7, 4)   |
| $S_3$           | (8, 3)   |
| $S_4$           | (9, 4)   |
| $S_5$           | (4, 2)   |

Now, let's say your task is to find the topper of the class (yup, as always, creating an unnecessary competition in a learning environment). Now, there may exist many personal opinions on how to rank them, like take the sum (or mean) of two score, take the minimum of two scores etc. But, all these are subjective and can not be generalized to any pair of exams. 
The main point is that there exist a pair of students, which cannot be compared.  
for ex. $S_1$ and $S_3$ cannot be compared, similarly $S_1$ and $S_2$ cannot be compared, but there are some pairs which can be compared, like $S_1$ and $S_5$, as $S_1$ scored higher marks in both the subjects.

There exists many such examples, for ex. when you are deciding which laptop to buy, you may consider several features for comparing them, like: price, RAM size, screen size, processor, company etc.   

- For representing the order between elements, we use the $\leq$ symbol (seems intuitive), 
so we can say that $S_5 \leq S_1$, $S_3 \leq S_4$ and so on.   
- We say that elements $a, b$ of a poset are comparable, if either $a \leq b$ or $b \leq a$.
- Although the symbol is of $\leq$, but the binary relation can be of any sort,  
formally, a partial order is any binary relation that is reflexive (each element is comparable to itself), antisymmetric (no two different elements precede each other), and transitive.  
[Complete definition can be obtained here](https://en.wikipedia.org/wiki/Partially_ordered_set)  

### Representing poset as a Directed Acyclic Graph (DAG)



### Linear extensions of a poset



### Naive recursive algorithm for Counting Linear Extensions



## Relation to volume computation





