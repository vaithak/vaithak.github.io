---
layout: single
title:  "Automatic Differentiation"
date:   2023-05-14
tag: autodiff
toc: true
toc_sticky: true
header:
  teaser: /assets/images/llvm.png
---

Given a continuos function $f(x)$, how do you compute it's derivative $f'(x_0)$  for a particular input $x_0$ computationally?  
Similarly, for a vector valued function $f(\vec{\boldsymbol{x}})$, how do we compute its gradient $\nabla_{\vec{\boldsymbol{x_0}}} f(\vec{\boldsymbol{x}})$?
Where $\vec{\boldsymbol{x}}$ is an $n$ dimensional vector and  

$$
  \nabla f(\vec{\boldsymbol{x}}) = [ \frac{\partial f}{\partial x_0},  \frac{\partial f}{\partial x_1}, \dots \frac{\partial f}{\partial x_n}]^T
$$


Computing these is beneficial in many applications - for example: 
- Training of parameterized ML models (including Deep Neural Networks): this involves finding the optimal set of value of parameters $\boldsymbol{\hat{\theta}}$ which minimizes a particular loss function function $\mathcal{L}$ given a particular set of training examples $\boldsymbol{X}$ and corresponding labels $\boldsymbol{y}$, i.e:

$$
\begin{equation}
  \boldsymbol{\hat{\theta}} = \arg\_{\theta} \min \mathcal{L} (\boldsymbol{X}, \boldsymbol{y}, \boldsymbol{\theta})
\end{equation}
$$

To solve the above optimization problem, we use some variant of gradient descent which exploits the  property of gradient that it always points in the direction of maximum increase.  
The optimization algorithm has the following iterative form:

$$
\begin{equation}
  \boldsymbol{\hat{\theta}}\_{t+1} = \boldsymbol{\hat{\theta}}\_{t} - \eta \nabla\_{\hat{\theta}\_t}\mathcal{L} (\boldsymbol{X}, \boldsymbol{y},  \theta)
\end{equation}
$$

The above equation essentially states that at each time step we move a little bit in the negative direction of the loss function's gradient at the current estimate.


## Numerical Differentiation
Well, we can start by the definition of the derivative:

$$
f'(x_0) = \lim_{h \rightarrow 0} \frac{f(x_0+h) - f(x_0)}{(x_0+h) - x} = \lim_{h \rightarrow 0} \frac{f(x_0+h) - f(x_0)}{h}
$$

Now, using the above formulation we can compute the function $f$ at very small values of $h$, for example: $10^{-5}$. 

This seems to be a good solution for cases where $f$ is sort of a black box function and we don't have access to its inner details/compositions. But it has a major flaw: 
> for computing gradient of a $n$ dimensional vector valued function, it requires $2n$ computations of function $f$. 

This can be particularly expensive for cases where $n$ is large, which is definitely the case for Deep Neural Networks based models.



## Symbolic Differentiation
Treating $f$ as a black-box didn't work out, this means we need to exploit how the function works internally - what are it's individual smaller subfunctions for which $f$ is the composition. 

For example: 
$$
\begin{align}
\text{if } f(x) &= \log(x)\sin^2(x), \newline 
\Rightarrow f(x)  &= f_1(x) \times f_2(f_3(x)) \newline
\text{where } f_1(x) &= \log(x), f_2(x) = x^2, f_3(x) = \sin(x)
\end{align}
$$

Using the above structure, we can use the properties of derivatives like product rule and chain rule to compute the derivative of the complete function $f$ using the derivatives of $f_1, f_2, f_3$.
This way we can get an exact expression of the derivative function $f'(x)$ using a rule based engine as follows:
<center>
    <img src="/assets/images/sym-diff.png" width="85%" /><br>
    <em style="font-size: 0.7em"> Rules for symbolic differentiation</em>
</center>
<br>
This is what is used by tools like Wolfram Alpha.  

- This seems like a perfect solution but the question is: do we want the complete expression of the derivative? For example: if $f$ is a product of $n$ such functions, then the derivative expression will contain $\approx 2^n$ terms, some of which may be redundant computations.
One faces a similar problem when differentiating deep neural networks, which consists of composing many layers on top of each other.
- Although this method works for purely mathematical functions, but what about handling control flow? i.e how do we handle *if* conditions and *for* loops.

The aim is to get a procedure for computing the

## Automatic Differentiation

