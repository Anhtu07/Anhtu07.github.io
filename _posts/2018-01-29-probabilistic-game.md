---
layout: post
title:  "Solving a Probabilistic Problem"
date:   2018-01-29
excerpt: "This article presents the algebraic proof and method for solving a two-gamers problem"
image: "/images/prob_model.png"
---

## The Problem

Suppose there are two players A and B. A owns $a$ dollars, while B owns $b$ dollars ($a, b \in \mathbb{N}$). Each turn of play, A has $p$ chance of winning and $q = 1-p$ chance of losing. if A win, he gets a dollar; if he loses, he lose one dollar. The game end when either of players is out of money. The question is what probability A will win (A gets $a+b$ dollar) or A will lose (A has 0 dollars).

## The Model

This problem can be easily put into a mathematical model as follows. Let $\{X_n\}%_$ be a random variable sequence such that $X_i$ is the money that A owns at $i^{th}$ turn of the game. We can easily obtain the transition matrix $P$:

$$\begin{bmatrix} 
1 & 0 & 0 & \dots & 0 \\ 
q & 0 & p & \dots & 0 \\
0 & q & 0 & \dots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \dots & 1 
\end{bmatrix}$$

$X_i$ can be a element of the set $I = $ \{$1,2,3,.., a+b$\} and $p_{ij}$ is the probability that state $X_i$ will go to state $X_j$.

##  The Notation
We only need to find the probability that starting $a$ dollars, A will end up at having $a+b$ dollars after some turn (the other case is similiar). Let H equal inf \{$n \geq 0 | X_n = a+b$\}, this definition also means that $H$ is a random variable .Let define $h_i$ as follow $ h_i = P(H < \infty | X_0 = iv) $

## The Theorem
The set of $h_i$ with $i = \overline{1,a+b}$ is the non-negative smallest solution of the following system

$$
\left\{
\begin{array}{l}
h_i = 1 \ \ \ \ \ \ \ \ \ \ \ \ \ \ when \ i = a+b \\
h_i = \sum_{j \in I} p_{ij} h_j \ when \ i \neq a+b
\end{array}
\right.
\ \ \ \ \ \ \ (\ast)
$$

The smallest solution here means that if $x$ is also non-negetive solution of the system $*$ then $x_i \geq h_i \forall i \in I$

### Proof
We first prove that $h = (h_i)_{i \in I}$ is a solution of *.
If $i = a+b$ then 

$$h_i = P(H < \infty | X_0 = a+b) = P(0 < \infty | x_0 = a+b) = 1$$

If $i \neq a+b$ then

$$h_i = P(H < \infty | X_0 = i) = \sum_{j \in I} P(H < \infty | X_1 = j) \cdot P(X_i = j | X_0 = i) $$

Since $$P(H < \infty \mid X_1 = j) = P(H < \infty \mid X_0 = j) = h_j$$. We conclude that

$$h_i = \sum_{j \in I} p_{ij} h_j $$


To this point, we have proven that $$h = (h_i)_{i \in I}$$ is a solution of system $\ast$. Now we will prove that $h$ is the smallest solution of $\ast$. Suppose $$x = (x_i)_{i \in I}$$ is a non-negative solution of $\ast$. Clearly, $x_{a+b} = 1$. With $i \neq a+b$, we have

$$
x_i = \sum_{j \in I} p_{ij}x_j =  p_{i, a+b} + \sum_{j \neq a+b} p_{ij}x_j \\
= p_{i, a+b} + \sum_{j \neq a+b} p_{ij} (p_{j, a+b} + \sum_{r \neq a+b } p_{jr}x_r) \\
= P_i(X_1 = a+b) + P(X_1 \neq a+b, X_2 = a+b) + \sum_{j \neq a+b} \sum_{r \neq a+b} p_{ij}p_{jr}x_r
$$

If we continue to expand $x_r$ as above, we can derive that

$$
P_i(X_1 = a+b) + ... + P_i(X_1 \neq a+b, X_2 \neq a+b, ... X_n = a+b) + \sum_{j_1 \neq a+b} ... \sum_{j_n \neq a+b} p_{ij_1}p{j_1j_2}...p_{j_{n-1}j_{n}}x_{jn} \\
= P_i(H \le n) + \sum_{j_1 \neq a+b} ... \sum{j_n \neq a+b} p_{ij_1}p{j_1j_2}...p_{j_{n-1}j_{n}}x_{jn}
$$

The "bunch of sum" in the above formula is used for nothing but it is greater than $0$. $$x_i > P_i(H \le n) \forall n$$. So $$x_i \geq h_i \forall i \in I$$.

In conclusion, $h$ is is the non-negative smallest solution of the system *

### The Formula

Back to our original problem, to calculate the chance of A wins starting from $a$ dollars is to find $h_a$ in the above system. By the above theorem, we need to solve the following system

$$
\left\{
\begin{array}{l}
h_0 = 0 \\
h_{a+b} = 1 \\
h_i = q h_{i-1} + p h_{i + 1} \forall i = \overline{1, a+b-1}
\end{array}
\right.
$$

we rewrite our recursive formula as $h_{i+1} = \frac{1}{p} h_i - \frac{q}{p} h_{i+1}$. From this we can derive our characteristic polynomial

$$x^2 - \frac{1}{p} x + \frac{q}{p}$$ 


We have two solution of the characteristic polynomial is

$$x_{1,2} = \frac{\frac{1}{p} \pm \sqrt{\frac{1}{p^2} - 4\frac{q}{p}}}{2} = \frac{\frac{1}{p} \pm \sqrt{\frac{1}{p^2} - 4\frac{1}{p} + 4}}{2} = \frac{\frac{1}{p} \pm \left\lvert\frac{1}{p}-2\right\lvert}{2}$$

In this problems, $p = 0$ or $1$ is trivial since if so, A will always lose or win at every turn. Therefore we will only consider $p, q \neq 0, 1$.

If $p \neq q \neq \frac{1}{2}$ then the above characteristic polynomial has exactly two distinct solution. However we need not to consider which one of \{$p, q$\} is greater than $\frac{1}{2}$ because in both case, the polynomial has two solution $1$ and $\frac{q}{p}$. Our general formula will have the form

$$h_n = c_1 (\frac{q}{p})^n + c_2$$

where $C_1$ and $c_2$ is two real constant satifying the initial conditions

$$
\left\{
\begin{array}{l}
h_0 = c_1 + c_2 = 0 \\
h_{a+b} = c_1 (\frac{q}{p})^{a+b} + c_2 = 1 \\
\end{array}
\right.
$$

Solving this equation, we easily obtain that $c_1 = \frac{-1}{1-(\frac{q}{p})^{a+b}}$ and $c_2 = \frac{1}{1-(\frac{q}{p})^{a+b}}$. Substitute $c_1$ and $c_2$ in our general formula

$$h_n = \frac{1 - (\frac{q}{p})^n}{1-(\frac{q}{p})^{a+b}}$$

This means that, the chance of winning for A will be $h_a = \frac{1 - (\frac{q}{p})^a}{1-(\frac{q}{p})^{a+b}}$

Finally, in case $p = q = \frac{1}{2}$, $h_a = \frac{a}{a+b}$

### Example
Let consider a actual games where both A and B has 500 dollars. If both A and B has equally 50-50 chance of gaining money each turn, then from the above formula they have 50% chance of winning the whole game. However, if we just slightly favor A, giving him 0.502 chance of winning each turn, then surprisingly A will have approximately 99% of winning the whole game.

Despite the high winning chance, a simple simulation of the game shows that it takes A millions of steps to win the whole game. Now, we may ask what the expected number of step of the game. In fact, we can easily construct a system that solves the expection of number of step similiar to system that solves the probability. Let $k_a = E(H \mid X_0 = a)$ then

$$
\left\{
\begin{array}{l}
k_{a+b} = 0 \\
k_i = 1 + \sum{j \neq a+b} p_{ij} k_j \ when \ i \neq {a+b}
\end{array}
\right.
$$

Last words, this problem can be generalized into the problem [Markov Chain](https://en.wikipedia.org/wiki/Markov_chain) reaching its [Absorbing State](https://en.wikipedia.org/wiki/Absorbing_Markov_chain)




















