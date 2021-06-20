---
layout: single
title:  "GSoC: About the project"
date:   2021-06-21
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
One graphical way of representing the poset is by using a directed graph. \\
This is done by representing each element of the poset by a node and there will be a directed edge between every pair of nodes $a \rightarrow b$ for which $a \leq b$.
<center>
    <img src="/assets/images/poset-dag-self-loops.png" width="70%" /><br>
    <em style="font-size: 0.7em"> Directed (not yet acyclic) Graph representing the example poset </em>
</center>
<br>

As can be easily seen in the above graph, it is not yet acyclic because of the self loops.  \\
This can be solved easily, as we know that for every element $a$ in the poset, $a \leq a$ will always be true, hence we can simply remove the self loops 
for a more compact representation. \\
This results in the following graph:
<center>
    <img src="/assets/images/poset-dag.png" width="55%" /><br>
    <em style="font-size: 0.7em"> DAG representation of the example poset </em>
</center>
<br>

Now, although the above graph is already a DAG, but if you observe carefully, it can be made even more compact. \\
This can be done by utilizing the transitive property of the partial order relations, i.e  

$$
\begin{align*}
  a \leq b, b \leq c \Rightarrow a \leq c
\end{align*}
$$

This means that in the above graph, when there is already an edge from $S_5 \rightarrow S_3$ and then $S_3 \rightarrow S_4$, then the edge $S_5 \rightarrow S_4$ 
is not giving us any extra information, this means it is implicit and can be removed.  \\
This is known as finding the transitive reduction of a DAG, you can read more about it from [here](https://en.wikipedia.org/wiki/Transitive_reduction).

<center>
    <img src="/assets/images/poset-dag-transitive.png" width="55%" /><br>
    <em style="font-size: 0.7em"> Transitive Reduction of the above DAG </em>
</center>
<br>

### Linear extensions of a poset
Let's say we want to assign ranks to the students in our example, but didn't we just discuss that there is ambiguity in that :thinking: ??   

Still, let's say we have received a strict order from the principal of this school to assign ranks to the students, now you being a 
teacher with good ethics decides to assign ranks with a condition, if a student $b$ has better marks than student $a$ in both exams, 
i.e $a \leq b$ in the poset of the students, then definitely $b$ will get a better rank than $a$.  

The following are 2 example rankings that you can assign to the students (the right one being rank 1 and left one being rank 5):

$$
\begin{equation}
  \begin{split}
    & S_5 \leq S_2 \leq S_3 \leq S_4 \leq S_1  \\
    & S_5 \leq S_1 \leq S_3 \leq S_2 \leq S_4  \\
  \end{split}
\end{equation}
$$

The above example rankings are examples of a [total order](https://en.wikipedia.org/wiki/Total_order), the only additional property of a *"total order or a linear order"* 
compared to a *"partial order"* is that any two elements are comparable.    

Finally coming to definition of a linear extension:
> A linear extension of a partial order is a total order or a linear order that is compatible with the partial order.

In the above definition, compatible means that if $a \leq b$ in the partial order, $\Rightarrow a \leq b$ in the extended total order.  

A genuine question that may arise in your mind , what is so linear about it? :confused:  
<em style="font-size: 0.8em"> Hint: think about how will the DAG representation of a totally ordered set look like after transitive reduction. </em>
> HW: Prove that the number of linear extensions of a poset is equal to the number of topological orderings of it's corresponding DAG.

### Naive recursive algorithm for Counting Linear Extensions
Now that we know what a linear extension is, let's devise a basic algorithm for counting the exact number of linear extensions. 
- Even before thinking about an algorithm, let's think about the scale of this number? For this, let's take the worst case example, i.e a set of $n$ elements
with no order between any pair of elements, it is easy to see that there are $n!$ number of linear extensions for this set. Thus, we see that the count of 
linear extensions can be very high ($n! \propto \sqrt{n} \left(\frac{n}{e}\right)^n$).
- Now, 


## Relation to volume computation
I hope you enjoyed the post so far, but as you know the library `volesti` is used for volume computations of convex bodies and the problem 
seems to lie in the **combinatorial domain**, what does it have to do with the volume computations ? :thinking:   

Keep thinking, this question will be discussed in the next post :wink:
