---
layout: post
title: "Pattern formation in animals"
date: 2014-04-13 12:00:00 -0700
category: numerical-simulation
---

Reaction-diffusion equations are used to explore the pattern formation
in animals.  The general equations are based on organic chemicals
called activators and inhibitors.  The coupled equation can generate a
variety of patterns by varying the model parameters,

$$\begin{aligned}
\dfrac{\partial u}{\partial t} &= \nabla^2 u + \gamma \left( a - u - \dfrac{\rho v}{1 + u + k u^2} \right) \\
\dfrac{\partial v}{\partial t} &= d \nabla^2 v + \gamma \left( \alpha \left( b - v \right) - \dfrac{\rho v}{1 + u + k u^2} \right)
\end{aligned}$$

A sample of the output of these equations is shown below using
parameter \\(\alpha = 1.5\\), \\(k = 0.1\\), \\(\rho = 18.5\\), \\(a = 92\\), \\(b =
64\\), \\(d = 10\\), \\(\gamma = 15\\).  The boundary conditions are Newmann
condition in the x-direction and periodic in y.  The equations are
time advanced using a simple explicit center time difference scheme.

| ![](/assets/patf_pery_g15.png) |
|:--:|
| *Simulated reaction-diffusion equations over a square domain with periodic boundary conditions in the y-direction.* |

---
## Reference
J.D. Murray, [A Pre-pattern Formation Mechanism for Animal Coat Markings](https://doi.org/10.1016/0022-5193(81)90334-9), J. theor. Biol. 88 (1), 1981, 161 - 199.
