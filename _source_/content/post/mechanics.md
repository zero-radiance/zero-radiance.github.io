---
title: "Mechanics"
date: 2023-10-27T16:15:35-07:00
draft: true
---

[Newtons's 2nd & 3rd laws](https://en.wikipedia.org/wiki/Newton%27s_laws_of_motion):
$\bm{F} = d\bm{p}/dt, \space \bm{F_{ij}} = -\bm{F_{ji}}$

Kinetic energy (massive particle):
$T = \bm{p}^2/(2 m)$

[Work-energy theorem](https://www.feynmanlectures.caltech.edu/I_13.html):
$W = T_2 - T_1 = \int_1^2 \bm{F} \cdot d\bm{r}$

[Conservation of energy](https://www.feynmanlectures.caltech.edu/I_13.html):
$E = \sum E_i = \sum_i \left( T_i + \sum_{j<i} U_{ij} \right), \space dE/dt = 0$

[Conservative force (field)](https://en.wikipedia.org/wiki/Conservative_force):
$\nabla \times \bm{F} = 0 \implies \bm{F} = -\nabla U, \space W = U_1 - U_2$, energy is conserved

[Scalar potential (field)](https://www.feynmanlectures.caltech.edu/I_14.html):
$U = k V, \space \bm{F} = k \bm{G}, \space \bm{G} = -\nabla V$

[Electrostatic potential](https://en.wikipedia.org/wiki/Electric_potential):
$V_j(\bm{r_i}) = k_e q_j r_{ij}^{-1}, \space U_{ij} = q_i V_j = k_e q_i q_j r_{ij}^{-1}, \space \bm{F_{ij}} = q_i \bm{E_j} = k_e q_i q_j \bm{\hat{r}\_{ij}} r_{ij}^{-2}$

[Gravitational potential](https://en.wikipedia.org/wiki/Gravitational_potential):
$V_j(\bm{r_i}) = -G m_j r_{ij}^{-1}, \space U_{ij} = m_i V_j = -G m_i m_j r_{ij}^{-1}, \space \bm{F_{ij}} = m_i \bm{G_j} = -G m_i m_j \bm{\hat{r}\_{ij}} r_{ij}^{-2}$

Earth's potential:
$V(y) = g y, \space U = m V = m g y, \space F = -dU/dy = -m g$

String's potential:
$V(x) = x^2/2, \space U = k V = k x^2/2, \space F = -dU/dx = -k x$

Friction (non-conservative):
$F = -\mu N = -\mu m g \cos{\theta}$

[Parameterized curve](https://en.wikipedia.org/wiki/Taylor's_theorem):
$\bm{r} = \bm{r}(s) = \bm{r}(a) + \bm{t}(a) (s - a) + \bm{n}(a) (s - a)^2/2 + ...$

Infinitesimal displacement:
$d\bm{r} = d\bm{r} / ds \cdot ds = \bm{t} ds, \space d\bm{r} \cdot d\bm{r} = \bm{t}^2 ds^2$

[Length element](https://en.wikipedia.org/wiki/Line_element):
$ds = (d\bm{r} \cdot d\bm{r})^{1/2}$

[Tangent vector](https://en.wikipedia.org/wiki/Tangent_vector):
$\bm{t} = d\bm{r} / ds, \space \vert \bm{t} \vert = 1$

[Normal vector](https://en.wikipedia.org/wiki/Normal_(geometry)):
$\bm{n} = d^2\bm{r} / ds^2 = d\bm{t} / ds, \space \vert \bm{n} \vert = 1/R, \space \bm{t} \cdot \bm{n} = 0$

[Gradient](https://en.wikipedia.org/wiki/Gradient):
$\nabla f = \partial f / \partial \bm{r}$

[Directional derivative](https://en.wikipedia.org/wiki/Directional_derivative):
$\partial f/\partial s = \partial f / \partial \bm{r} \cdot d\bm{r} / ds = \nabla f \cdot \bm{t}$

Area element:
$?$

Volume element:
$?$

[Motion in polar coordinates](https://en.wikipedia.org/wiki/Mechanics_of_planar_particle_motion#Polar_coordinates_in_an_inertial_frame_of_reference):
$\bm{r} = \bm{r}(t) = \[ x(t), y(t) \]^{\Tau} = r(t) \[ \cos{\varphi(t)}, \sin{\varphi(t)} \]^{\Tau} = r(t) \bm{\hat{r}}(t)$

$\bm{\hat{r}} = \partial \bm{r} / \partial r = -d\bm{\hat{\varphi}} / d\varphi = \[ \cos{\varphi}, \sin{\varphi} \]^{\Tau}, \space \vert \bm{\hat{r}} \vert = 1$

$\bm{\hat{\varphi}} = d\bm{\hat{r}} / d\varphi = \[ -\sin{\varphi}, \cos{\varphi} \]^{\Tau}, \space \vert \bm{\hat{\varphi}} \vert = 1, \space \bm{\hat{r}} \cdot \bm{\hat{\varphi}} = 0$

$d\bm{\hat{r}} / dt = d\bm{\hat{r}} / d\varphi \cdot d\varphi / dt = \dot{\varphi} \bm{\hat{\varphi}} = \omega \bm{\hat{\varphi}}$

$d\bm{\hat{\varphi}} / dt = d\bm{\hat{\varphi}} / d\varphi \cdot d\varphi / dt = -\dot{\varphi} \bm{\hat{r}} = -\omega \bm{\hat{r}}$

$\bm{v} = d\bm{r} / dt = \dot{r} \bm{\hat{r}} + r \omega \bm{\hat{\varphi}} = \bm{v_{rad}} + \bm{v_{tan}}$

$\bm{a} = d\bm{v} / dt = \left( \ddot{r} \bm{\hat{r}} + \dot{r} \omega \bm{\hat{\varphi}} \right) + \left( \dot{r} \omega \bm{\hat{\varphi}} + r \dot{\omega} \bm{\hat{\varphi}} - r \omega^2 \bm{\hat{r}} \right) = \left( \ddot{r} - r \omega^2 \right) \bm{\hat{r}} + \left( 2 \dot{r} \omega + r \dot{\omega} \right) \bm{\hat{\varphi}} = \bm{a_{rad}} + \bm{a_{tan}}$

$\bm{F} = m \bm{a} = \left( m \ddot{r} - m r \omega^2 \right) \bm{\hat{r}} + \left( 2 m \dot{r} \omega + m r \dot{\omega} \right) \bm{\hat{\varphi}}$

$\bm{F} + m r \omega^2 \bm{\hat{r}} - 2 m \dot{r} \omega \bm{\hat{\varphi}} = m \bm{\tilde{a}} = m \ddot{r} \bm{\hat{r}} + m r \ddot{\varphi} \bm{\hat{\varphi}}$

$\bm{F_{cen}} = m r \omega^2 \bm{\hat{r}}, \space \bm{F_{cor}} = - 2 m \dot{r} \omega \bm{\hat{\varphi}}$


