---
title: "Mechanics"
date: 2023-10-27T16:15:35-07:00
draft: true
---

[Newtons's 2nd & 3rd laws](https://en.wikipedia.org/wiki/Newton%27s_laws_of_motion):
$\bar{F} = d\bar{p}/dt, \space \bar{F}\_{ij} = -\bar{F}\_{ji}$

Linear momentum (conserved): $\bar{p} = m \bar{v}, \space d\bar{p}/dt = \bar{F}$

Angular momentum (conserved):
$\bar{L} = I \bar{\omega} = \bar{r} \times \bar{p} = r \bar{p}\_{tan}, \space d\bar{L}/dt = \bar{\tau} = \bar{r} \times \bar{F} = r \bar{F}\_{tan}$

Moment of inertia:
$I = m (\bar{r} \times \hat{\omega})^2 = m (r \sin{\theta})^2$

Kinetic energy:
$T = p^2/(2 m) = p_{rad}^2/(2 m) + L^2/(2 I)$

[Work-energy theorem](https://www.feynmanlectures.caltech.edu/I_13.html):
$W = T_2 - T_1 = \int_1^2 d\bar{r} \cdot \bar{F} = \int_1^2 dr F_{rad} + \int_1^2 d\varphi \tau$

[Total energy (conserved)](https://www.feynmanlectures.caltech.edu/I_13.html):
$E = \sum E_i = \sum_i \left( T_i + \sum_{j<i} U_{ij} \right), \space dE = dW_{non} = d\bar{r} \cdot \bar{F}\_{non}$

[Conservative force (field)](https://en.wikipedia.org/wiki/Conservative_force):
$\nabla \times \bar{F} = 0 \implies \bar{F} = -\nabla U, \space W = U_1 - U_2, \space E_1 = E_2$

[Scalar potential (field)](https://www.feynmanlectures.caltech.edu/I_14.html):
$U = k V, \space \bar{F} = k \bar{G}, \space \bar{G} = -\nabla V$

[Electrostatic potential](https://en.wikipedia.org/wiki/Electric_potential):
$V_j(\bar{r}\_i) = k_e q_j r_{ij}^{-1}, \space U_{ij} = q_i V_j = k_e q_i q_j r_{ij}^{-1}, \space \bar{F}\_{ij} = q_i \bar{E}\_j = k_e q_i q_j r_{ij}^{-2} \hat{r}\_{ij}$

[Gravitational potential](https://en.wikipedia.org/wiki/Gravitational_potential):
$V_j(\bar{r}\_i) = -G m_j r_{ij}^{-1}, \space U_{ij} = m_i V_j = -G m_i m_j r_{ij}^{-1}, \space \bar{F}\_{ij} = m_i \bar{G}\_j = -G m_i m_j r_{ij}^{-2} \hat{r}\_{ij}$

Earth's potential:
$V(y) = g y, \space U = m V = m g y, \space \bar{F} = -\nabla U = -m g \hat{y}$

String's potential:
$V(x) = x^2/2, \space U = k V = k x^2/2, \space \bar{F} = -\nabla U = -k x \hat{x}$

Friction (non-conservative):
$\bar{F} = -\mu N \hat{v}, \space N \text{ is the sum of all forces directed towards the surface}$

[Parameterized curve](https://en.wikipedia.org/wiki/Taylor's_theorem):
$\bar{r} = \bar{r}(s) = \bar{r}(a) + \bar{t}(a) (s - a) + \bar{n}(a) (s - a)^2/2 + ...$

Infinitesimal displacement:
$d\bar{r} = d\bar{r} / ds \cdot ds = \bar{t} ds, \space d\bar{r} \cdot d\bar{r} = t^2 ds^2$

[Length element](https://en.wikipedia.org/wiki/Line_element):
$ds = (d\bar{r} \cdot d\bar{r})^{1/2}$

[Tangent vector](https://en.wikipedia.org/wiki/Tangent_vector):
$\bar{t} = d\bar{r} / ds, \space \vert \bar{t} \vert = 1$

[Normal vector](https://en.wikipedia.org/wiki/Normal_(geometry)):
$\bar{n} = d^2\bar{r} / ds^2 = d\bar{t} / ds, \space \vert \bar{n} \vert = 1/R, \space \bar{t} \cdot \bar{n} = 0$

[Gradient](https://en.wikipedia.org/wiki/Gradient):
$\nabla f = \partial f / \partial \bar{r}$

[Directional derivative](https://en.wikipedia.org/wiki/Directional_derivative):
$\partial f/\partial s = \partial f / \partial \bar{r} \cdot d\bar{r} / ds = \nabla f \cdot \bar{t}$

Area element:
$?$

Volume element:
$?$

[Motion in polar coordinates](https://en.wikipedia.org/wiki/Mechanics_of_planar_particle_motion#Polar_coordinates_in_an_inertial_frame_of_reference):
$\bar{r} = \bar{r}(t) = \[ x(t), y(t) \]^{\mathsf{T}} = r(t) \[ \cos{\varphi(t)}, \sin{\varphi(t)} \]^{\mathsf{T}} = r(t) \hat{r}(\varphi(t))$

$\hat{r} = \partial \bar{r} / \partial r = -d\hat{\varphi} / d\varphi = \[ \cos{\varphi}, \sin{\varphi} \]^{\mathsf{T}}, \space \vert \hat{r} \vert = 1$

$\hat{\varphi} = d\hat{r} / d\varphi = \[ -\sin{\varphi}, \cos{\varphi} \]^{\mathsf{T}}, \space \vert \hat{\varphi} \vert = 1, \space \hat{r} \cdot \hat{\varphi} = 0$

$d\bar{r} = \partial \bar{r} / \partial r \cdot dr + \partial \bar{r} / \partial \varphi \cdot d\varphi = dr \hat{r} + r d\varphi \hat{\varphi}$

$d\hat{r} / dt = d\hat{r} / d\varphi \cdot d\varphi / dt = \dot{\varphi} \hat{\varphi} = \omega \hat{\varphi}$

$d\hat{\varphi} / dt = d\hat{\varphi} / d\varphi \cdot d\varphi / dt = -\dot{\varphi} \hat{r} = -\omega \hat{r}$

$\bar{v} = d\bar{r} / dt = \dot{r} \hat{r} + r \omega \hat{\varphi} = \bar{v}\_{rad} + \bar{v}\_{tan}, \space \bar{L} = m r^2 \omega \hat{\varphi}$

$\bar{a} = d\bar{v} / dt = \left( \ddot{r} - r \omega^2 \right) \hat{r} + \left( 2 \dot{r} \omega + r \dot{\omega} \right) \hat{\varphi} = \bar{a}\_{rad} + \bar{a}\_{tan}$

$\bar{F} = m \bar{a} = \left( m \ddot{r} - m r \omega^2 \right) \hat{r} + \left( 2 m \dot{r} \omega + m r \dot{\omega} \right) \hat{\varphi} = \bar{F}\_{rad} + \bar{F}\_{tan}, \space \bar{\tau} = \left( 2 m r \dot{r} \omega + m r^2 \dot{\omega} \right) \hat{\varphi}$

$\bar{F} + m r \omega^2 \hat{r} - 2 m \dot{r} \omega \hat{\varphi} = m \tilde{a} = m \ddot{r} \hat{r} + m r \ddot{\varphi} \hat{\varphi}$

$\bar{F}\_{cen} = m r \omega^2 \hat{r}, \space \bar{F}\_{cor} = - 2 m \dot{r} \omega \hat{\varphi}$

[Levi-Civita symbol](https://en.wikipedia.org/wiki/Levi-Civita_symbol): $\varepsilon_{ijk} \varepsilon_{ipq} = \delta_{jp} \delta_{kq} - \delta_{jq} \delta_{kp}$

[Inner product](https://en.wikipedia.org/wiki/Dot_product): $\bar{A} \cdot \bar{B} = \bar{A}^{\mathsf{T}} \bar{B} = \delta_{ij} A_i B_j = A_i B_i$

[Outer product](https://en.wikipedia.org/wiki/Outer_product): $\left( \bar{B} \otimes \bar{C} \right)\_{jk} = \left( \bar{B} \bar{C}^{\mathsf{T}} \right)\_{jk} = B_j C_k$

[Vector product](https://en.wikipedia.org/wiki/Cross_product): $\left( \bar{B} \times \bar{C} \right)\_i = \frac{1}{2} \varepsilon_{ijk} T_{jk} = \varepsilon_{ijk} B_j C_k, \space T_{jk} = \varepsilon_{ijk} \left( \bar{B} \times \bar{C} \right)\_i = B_j C_k - B_k C_j, \space \bar{\bar{\kern{-0.3ex}T}} = \bar{B} \otimes \bar{C} - \left( \bar{B} \otimes \bar{C} \right)^{\mathsf{T}} \text{ is an antisymmetric tensor}$

[Triple product](https://en.wikipedia.org/wiki/Triple_product): $\mathrm{det}\negmedspace\left[ \bar{A} \space \bar{B} \space \bar{C} \right] = \bar{A} \cdot \left( \bar{B} \times \bar{C} \right) = \varepsilon_{ijk} A_i B_j C_k$
