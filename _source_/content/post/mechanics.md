---
title: "Mechanics"
date: 2023-10-27T16:15:35-07:00
draft: true
---

[Newtons's 2nd & 3rd laws](https://en.wikipedia.org/wiki/Newton%27s_laws_of_motion):
$\bm{F} = d\bm{p}/dt, \space \bm{F_{ij}} = -\bm{F_{ji}}$

Kinetic energy (massive particle):
$T = p^2/(2 m)$

[Work-energy theorem](https://www.feynmanlectures.caltech.edu/I_13.html):
$W = T_2 - T_1 = \int_1^2 \bm{F} \cdot d\bm{s}$

[Conservation of energy](https://www.feynmanlectures.caltech.edu/I_13.html):
$E = \sum_{i=1}^{n} E_i = \sum_{i=1}^{n} \left( T_i + \sum_{j=1}^{i-1} U_{ij} \right), \space dE/dt = 0$

[Conservative force (field)](https://en.wikipedia.org/wiki/Conservative_force):
$\nabla \times \bm{F} = 0 \implies \bm{F} = -\nabla U, \space W = U_1 - U_2$, energy is conserved

[Scalar potential (field)](https://www.feynmanlectures.caltech.edu/I_14.html):
$U = k V, \space \bm{F} = -\nabla U = k \bm{C}, \space \bm{C} = -\nabla V$

[Electrostatic potential](https://en.wikipedia.org/wiki/Electric_potential):
$V_j(\bm{r_i}) = k_e q_j / r_{ij}, \space U_{ij} = q_i V_j = k_e q_i q_j / r_{ij}, \space \bm{F_{ij}} = q_i \bm{E_j} = k_e q_i q_j \bm{\hat{r}\_{ij}} / r_{ij}^2$

[Gravitational potential](https://en.wikipedia.org/wiki/Gravitational_potential):
$V_j(\bm{r_i}) = -G m_j / r_{ij}, \space U_{ij} = m_i V_j = -G m_i m_j / r_{ij}, \space \bm{F_{ij}} = m_i \bm{G_j} = -G m_i m_j \bm{\hat{r}\_{ij}} / r_{ij}^2$

Friction (non-conservative): $F = -\mu N = -\mu m g \cos{\theta}$