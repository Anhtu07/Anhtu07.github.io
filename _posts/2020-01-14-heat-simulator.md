---
layout: post
title:  "Heat Simulation with FreeFEM"
date:   2020-01-14
excerpt: "Analyzing a simple heat simulation problem"
sitemap:
  lastmod: 2020-01-16
---

## The Problem
This series of blog will focus on how to simulate physical phenomena using finite element method. In the series, I will try not to mention too much about the well-defineness of these problems or the convergence rate of the algorithms. Instead, this blogs aim to give a simple construction and explanation of weak problem for some famous PDE equations.

The first blog in this series start with the heat equation. In the most general form, the heat equation can be written as follow:
$$
\begin{align}
\frac{\partial u}{\partial t} - \alpha \Delta &= f(X, t) \tag{1} \\
u(X, 0) &= u_0(X)_{| \Omega} \tag{2} \\
u(X, t) &= u_r(X, t)_{| \Gamma_1} \tag{3} \\
\frac{\partial u}{\partial n} (X, t) &= u_n(X, t)_{| \Gamma_2} \tag{4}
\end{align}
$$

Function $u$ represents the heat value for each point in space domain $\Omega$ (2 or 3 dimensions), where $X = [x, y]$ is a point in two-dimensional space (or $X = [x, y, z]$ in three-dimensional space), and $t$ is time variable. Equation (2) is called initial condition, (3) is Dirichlet boundary condition, and (4) is Neumann boundary condition. In this notation, $\Gamma_1$ and $\Gamma_2$ is part of the boundary $\partial \Omega$ satisfying $\Gamma_1 \cup \Gamma_2 = \partial \Omega$. And, $\Delta u$ is the Laplacian operator and is defined as:
$$
\Delta u = \frac{\partial^2 u}{\partial x^2} + \frac{\partial^2 u}{\partial y^2}
$$
The Laplacian operator at point $X\_0 = (x\_0, y\_0)$ can be interpreted as the average different between the value of $u$ at $X_0$ and the value of its neighbors. In heat diffusion context, we know that heat will move form hotter to colder places, which explain for the left hand side of (1) , where $\alpha$ be diffusivity coefficient (the higher value of $\alpha$, the more resistant to heat of the medium). The right hand side of (1) is the external power (wattage) given to each point in domain $\Omega$ overtime. In case $f = 0$, the heat is allowed to move freely with respect to three constraint (2), (3) and (4). In mathematical context, these condition is required for the uniqueness of $u$. However, each condition has its own physical meaning. Suppose we want to simulate how heat move if we put two $80^o C$conductors in a $20^o C$ room. Initially, every point in the room, except in the conductor, has value of $20^o C$, and we model the initial condition as $u(X, 0) = 20$ on the region not belonging to the conductors'. If we want to keep the heat of the conductor at a constant $80^o C$, then this mean we do not care about the internal point of them and set $u_r(X, t) =  80^o C$ on boundary of two conductors. Finally Neumann condition represent how much external heat is put through the boundary of $\Omega$,  for example if $u_n(X, t)$ is set to be zero on the boundary of the room, then it means there is no heat coming in or coming out of the room. The 2-D simulation of this phenomenon is conducted in the final section of this blog

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
border C1(t=0,1){x=-5+t;y=4;label=100;}
border C2(t=0,8){x=-4;y=4-t;label=100;}
border C3(t=0,1){x=-4-t;y=-4;label=100;}
border C4(t=0,8){x=-5;y=-4+t;label=100;}

border C5(t=0,1){x=5+t;y=4;label=101;}
border C6(t=0,8){x=6;y=4-t;label=101;}
border C7(t=0,1){x=6-t;y=-4;label=101;}
border C8(t=0,8){x=5;y=-4+t;label=101;}

border C9(t=0,2){x=10*cos(pi*t);y=10*sin(pi*t);label=102;}
real n = 100;
mesh Th = buildmesh(C1(n) + C2(n) + C3(n) + C4(n) + C5(n) + 
C6(n) + C7(n) + C8(n) + C9(n));
fespace Vh(Th,P1);
plot(Th);

func k = (x<=0.25 && x>=-0.25)*2 + 1;
Vh kappa = k, u, v, u0;

u0 = 0;

real dt = 0.02;
real T = 1;
int t;
int count = 0;
for (t = 1; t <= T/dt; t ++) {
	solve heat(u, v) = int2d(Th)(u*v + dx(u)*dx(v)*dt*kappa + dy(u)*dy(v)*dt*kappa)

	- int2d(Th)(u0*v)
	+ on(100,u=50) + on(101,u=80) + on(102,u=20);
	u0 = u;
	count ++;
	plot(u,fill=1, value = 1, ps="./heat simulation/sol_"+count+".jpg");
}
```

![Heat Simulation](\img\heat-simulation.gif)






