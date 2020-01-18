---
layout: post
title:  "Elasticity Simulation with FreeFEM"
date:   2020-01-18
excerpt: "Analyzing a simple system of elastic equations"
sitemap:
  lastmod: 2020-01-18
---

## The Problem
This blog continues the Finite Element Methods series for some simple physical phenomena simulation. Previously, I have covered the heat equation. In this post, I want to discuss another famous  problem: Elasticity. Concretely, we want to know how object of material deforms under pressure or external force. I will first state the system equation of elasticity before I try to explain what it means (I come from mathematics background, so many concepts in physics appear a little confusing to me)
$$
\begin{align}
-div(Ae(u)) & = f \tag{1} \\
u_{| \Gamma_D} & = 0 \tag{2} \\
(Ae(u)).n_{| \Gamma_N} & = g \tag{3}\\
\end{align}
$$

Denote
$$
A \psi = 2 \mu \psi + \lambda (\text{Tr}(\psi)) I_d ,
$$
and
$$
e(u) = \frac{1}{2} (\nabla u^T + \nabla u) ,
$$
where $\mu$ and $\lambda$ are call Lam$\'e$ parameters and are defined as follow:
$$
\mu = \frac{E}{2(1 + \nu)} \\
\lambda = \frac{E\nu}{(1+\nu)(1 - 2\nu)}
$$
 $E$ is [Young's modulus]([https://en.wikipedia.org/wiki/Young%27s_modulus](https://en.wikipedia.org/wiki/Young's_modulus)) which represents for the stiffness of solid material, and $\nu$ is called [Poisson's ratio](https://en.wikipedia.org/wiki/Poisson's_ratio) which explains for the phenomenon where the material extends in perpendicular with respect to direction of the force.  $u$ in the system of equation is the direction for which each particle in the object will move under force $f$ and external force on $g$ some boundary $\Gamma_N$.  In fact, I do not really understand why equation (1) is true, but as far as I know the simulation I run on my computer seems right :). One final note before we are going to construct the weak formulation for this problem is the meaning for the boundary conditions. Dirichlet condition for this type of equations is always set to be 0, which means that boundary is kept fixed, which Neumann condition is the external force apply on the object's edges.



### How to construct weak solution ?

Numerical solution of heat equations relies on two schemes: discrete time scheme and finite elements method.  The idea is fairly simple, we first divide the time horizon $[0, T]$ into small parts, each parts has the size $\delta t$ ($t_i = i * \delta t$) and approximate $\frac{\partial u}{\partial t} = \frac{u^n(X) - u^{(n - 1)}(X)}{\delta t}$ (here, we denote $u^n(X) =  u(X, t = t_n)$ ), and then try to solve $u^n$ at each time step given we know the previous step $u^{n-1}. To do this, we use finite element methods to solve the following (Backward Euler):


$$
\begin{align}
 \frac{u^n(X) - u^{(n - 1)}(X)}{\delta t} - \Delta u^n & = f^n \tag{*} \\
 u^0(X) & = u_0 \\
 u(X, t)_{| \Gamma_1} & = 0 \\
\frac{\partial u}{\partial n} (X, t)_{| \Gamma_2} &= u_n(X, t)
\end{align}
$$


The main idea of finite element methods is to partition the domain $\Omega$ into triangles (each triangle is called an element),  each triangle will have a corresponding basis function (commonly linear function). The function $u^n$ will be the linear combination of these basis functions.  When we have the mesh (partition of the domain), and choose a function space to approximate $u$, the only work left is to find the coefficient for each basis functions. Finding these coefficient, in fact, is equivalent to solve system of linear equation. And this equation comes from the construction of weak problem. Let test function $v$ be a function whose value on $\Gamma_1$ equals 0.
$$
\begin{align}
\int_{\Omega}\left(\frac{u^n(X) - u^{(n - 1)}(X)}{\delta t}v \right) - \int_\Omega \Delta u^n v & = \int_\Omega f^n v \\
\int_\Omega \frac{u^n v}{\delta t} + \int_\Omega \nabla u^n \nabla v & = \int_\Omega \frac{u^{n-1} v}{\delta t} + \int_\Omega fv + \int_{\partial \Omega} \frac{\partial u^n}{\partial n} v \\
\int_\Omega \frac{u^n v}{\delta t} + \int_\Omega \nabla u^n \nabla v - \int_{\Gamma_2} u_n(X, t_n)v & = \int_\Omega \frac{u^{n-1} v}{\delta t} + \int_\Omega fv \tag{5} \\
\end{align}
$$
The equation (5) can be shorten as $a(u, v) = L(v)$ where $a$ is bilinear operator, and $L$ is linear operator. The heat equation new asks for $u$ satisfy (5) with every $v$ in the chosen function space. The problem has unique solutions according to Lax-Milgram Theorem.

And that's basically everything we need to use freefem++ to model the heat flow ! I will cover a little more about finite element method in the subsequent blogs. For now, we will turn to simulate the phenomenon described earlier.

### Numerical solution 

The last time I implemented FEM for heat equation by python, and Stokes equations by Matlab was horrible experiences.  The most annoying parts are mesh triangulation and calculating values on stiffness and load matrix to satisfy all boundary conditions. However, I found everything is so simple with Freefem. Basically, there's only two step, '' draw " the domain and rewrite the weak problem. The following code is the simulation of physical phenomena described in the introduction. There's a minor change is that I put a wall divided the room into two half, and this wall have diffusivity constant 3 times greater than that of air.

```c++
real E = 21e5;
real nu = 0.28;
real f = -1e4;
 // Mesh
border C1(t = pi, 0){x = cos(t); y=2*sin(t);}
border C2(t = 0, 1){x = 1+t; y = 0;}
border C3(t = 0, pi){x = 2*cos(t); y = 3*sin(t);}
border C4(t = 0, 1){x = -2+t; y = 0;}
int n = 20;
mesh Th = buildmesh(C1(n) + C2(n) + C3(n) + C4(n));

plot(Th);
 // Fespace
fespace Vh(Th, P2);
Vh u, v;
Vh uu, vv;

 // Macro
real sqrt2=sqrt(2.);
macro epsilon(u1,u2) [dx(u1),dy(u2),(dy(u1)+dx(u2))/sqrt2] //
macro div(u,v) ( dx(u)+dy(v) ) //

// Problem
real mu= E/(2*(1+nu));
real lambda = E*nu/((1+nu)*(1-2*nu));
real T = 10;
real dt = 0.1;
int count = 0;
for(real t = 0; t <= T; t+=dt) {
solve lame([u, v], [uu, vv])
 = int2d(Th)( lambda * div(u, v) * div(uu, vv) 
	+ 2.*mu * (epsilon(u,v)' * epsilon(uu,vv)) ) - 
	int2d(Th)( f*vv )
 + on(C2, C4, u=0, v=0);
count ++;
// Plot
 real coef= 0.7;
 // Move mesh
Th = movemesh(Th, [x+u*coef, y+v*coef]);
plot(Th, ps="./elastic simulation/sol_"+count+".jpg");
}
```

![Heat Simulation](\img\elastic-simulation.gif)






