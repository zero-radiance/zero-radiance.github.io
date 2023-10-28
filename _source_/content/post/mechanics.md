---
title: "Mechanics"
date: 2023-10-27T16:15:35-07:00
draft: true
---

[Newtons's 2nd law](https://en.wikipedia.org/wiki/Newton%27s_laws_of_motion#Second_law):
$\bm{F} = d\bm{p}/dt$

[Newtons's 3rd law](https://en.wikipedia.org/wiki/Newton%27s_laws_of_motion#Third_law):
$\bm{F}\_{ij} = -\bm{F}\_{ji}$

[Conservation of energy](https://www.feynmanlectures.caltech.edu/I_13.html):
$E = \sum_{i=1}^{n} E_i = \sum_{i=1}^{n} \left( T_i + \sum_{j=1}^{i-1} U_{ij} \right), \space dE/dt = 0$

Kinetic energy:
$T = p^2/(2 m)$

[Work-energy theorem](https://www.feynmanlectures.caltech.edu/I_13.html):
$W = T_2 - T_1 = \int_1^2 \bm{F} \cdot d\bm{s}$

[Conservative force (field)](https://en.wikipedia.org/wiki/Conservative_force):
$\nabla \times \bm{F} = 0 \implies \bm{F} = -\nabla U, \space W = U_1 - U_2$, energy is conserved

[Potential (field)](https://www.feynmanlectures.caltech.edu/I_14.html):
$\space \bm{C} = -\nabla V, \space U = k V, \bm{F} = k \bm{C}$

[Electrostatic potential](https://en.wikipedia.org/wiki/Electric_potential):
$V_j(\bm{r}) = k_e q_j / \vert \bm{r} - \bm{r}\_j \vert, \space U_{ij} = q_i V_j(\bm{r_i}) = k_e q_i q_j / r_{ij}, \space \bm{F}\_{ij} = q_i (-\nabla V_j) = k_e q_i q_j \bm{\hat{r}}\_{ij}/r_{ij}^2$

[Gravitational potential](https://en.wikipedia.org/wiki/Gravitational_potential):
$V_j(\bm{r}) = -G m_j / \vert \bm{r} - \bm{r}\_j \vert, \space U_{ij} = m_i V_j(\bm{r_i}) = -G m_i m_j / r_{ij}, \space \bm{F}\_{ij} = m_i (-\nabla V_j) = -G m_i m_j \bm{\hat{r}}\_{ij}/r_{ij}^2$