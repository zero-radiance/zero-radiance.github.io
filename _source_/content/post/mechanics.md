---
title: "Mechanics"
date: 2023-10-27T16:15:35-07:00
draft: true
---

Newtons's laws of linear motion:
$\bar{F_i} = \bar{F}\_{i \thinspace ext} + \sum_j \bar{F}\_{ij} = d\bar{p}_i/dt, \space \bar{F}\_{ij} = -\bar{F}\_{ji}$

Newtons's laws of rotational motion:
$\bar{\tau_i} = \bar{\tau}\_{i \thinspace ext} + \sum_j \bar{\tau}\_{ij} = d\bar{L}_i/dt, \space \bar{\tau}\_{ij} = -\bar{\tau}\_{ji}$

*Without subscripts, the tensors are "absolute", e.i. relative to the origin. $\bar{d}$ is the "difference" vector.*

Linear momentum: $\bar{p} = m \bar{v} = m (\bar{v}\_{rad} + \bar{v}\_{tan}) = m (\bar{v}\_{rad} + \bar{\omega} \times \bar{r}), \space d\bar{p}/dt = \bar{F}$

Angular momentum:
$\bar{L} = \bar{r} \times \bar{p} = \bar{r} \times \bar{p}\_{tan} = -m \bar{r} \times \left( \bar{r} \times \bar{\omega} \right) = \bar{\bar{\kern{-0.3ex}I}} \bar{\omega}, \space d\bar{L}/dt = \bar{\tau} = \bar{r} \times \bar{F} = \bar{r} \times \bar{F}\_{tan}$

[Moment of inertia](https://www.feynmanlectures.caltech.edu/I_19.html):
$\bar{\bar{\kern{-0.3ex}I}} = -m \bar{r} \times \left( \bar{r} \times \bar{\bar{1}} \right) = m \left( (\bar{r} \cdot \bar{r}) \bar{\bar{1}} - \bar{r} \otimes \bar{r} \right)$ in general, or $I = m r^2$ in a plane

[Parallel axis theorem](https://en.wikipedia.org/wiki/Parallel_axis_theorem):
$\bar{\bar{\kern{-0.3ex}I}}\_R = \bar{\bar{\kern{-0.3ex}I}}\_{com} + M \left( (\bar{d} \cdot \bar{d}) \bar{\bar{1}} - \bar{d} \otimes \bar{d} \right)$ in general, or $I_R = I_{com} + M d^2$ in a plane, where $\bar{R} = \bar{R}\_{com} + \bar{d}$

*Inelastic collisions do not conserve kinetic energy. Momentum is always conserved.*

Kinetic energy:
$T = p^2/(2 m) = p_{rad}^2/(2 m) + L^2/(2 m r^2)$

[Work-energy theorem](https://www.feynmanlectures.caltech.edu/I_13.html):
$W = T_1 - T_0 = \int_0^1 d\bar{r} \cdot \bar{F} = \int_0^1 dr F_{rad} + \int_0^1 d\varphi \tau$

[Center of mass](https://www.feynmanlectures.caltech.edu/I_19.html):
$\bar{R}\_{com} = \sum_i m_i \bar{r}\_i / M, \space M = \sum_i m_i, \space \sum_i m_i \bar{d}\_i = 0$

Total linear momentum:
$\bar{P} = \sum_i m_i \bar{v}\_i = M d\bar{R}\_{com}/dt = M \bar{V}\_{com}, \space d\bar{P}/dt = \bar{F}\_{ext}$

Total angular momentum:
$\bar{L} = \sum_i \left( \bar{R} + \bar{d}\_i \right) \times \bar{p}\_i = \bar{R} \times \bar{P} + \sum_i \bar{d}\_i \times \bar{p}\_i, \space d\bar{L}/dt = \bar{\tau}\_{ext}$

[Total force](https://en.wikipedia.org/wiki/Rigid_body_dynamics#Newton's_second_law_in_three_dimensions):
$\bar{F} = \bar{F}\_{ext} = \sum_i \bar{F}\_{i \thinspace ext}$

[Total torque](https://en.wikipedia.org/wiki/Rigid_body_dynamics#Newton's_second_law_in_three_dimensions):
$\bar{\tau} = \bar{\tau}\_{ext} = \sum_i \bar{\tau}\_{i \thinspace ext} = \sum_i \left( \bar{R} + \bar{d}\_i \right) \times \bar{F}\_{i \thinspace ext} = \bar{R} \times \bar{F}\_{ext} + \sum_i \bar{d}\_i \times \bar{F}\_{i \thinspace ext}$

Invariant of a part of a rigid body:
if $\bar{r}\_i = \bar{R}\_{com} + \bar{d}\_i$ and $\bar{u}\_i = d(\bar{d}\_i)/dt$, then $d(d_i^2)/dt = 2 \bar{d}\_i \cdot \bar{u}\_i = 0$

[Velocity of a part of a rigid body](https://en.wikipedia.org/wiki/Moment_of_inertia#Motion_in_space_of_a_rigid_body,_and_the_inertia_matrix):
$\bar{v}\_i = \bar{V}\_{com} + \bar{u}\_i = \bar{V}\_{com} + \bar{\omega} \times \bar{d_i}$

[Acceleration of a part of a rigid body](https://en.wikipedia.org/wiki/Moment_of_inertia#Resultant_torque):
$\bar{a}\_i = \bar{A}\_{com} + \bar{\alpha} \times \bar{d_i} + \bar{\omega} \times \bar{u_i} = \bar{A}\_{com} + \bar{\alpha} \times \bar{d_i} + \bar{\omega} \times \bar{\omega} \times \bar{d_i}$

[Spin angular momentum of a rigid body](https://en.wikipedia.org/wiki/Moment_of_inertia#Angular_momentum_2):
$\bar{L}\_{spn} = \bar{L}\_{com} = \sum_i \bar{d}\_i \times \bar{p}\_i = \sum_i m_i \bar{d}\_i \times \bar{v}\_i = \bar{\bar{\kern{-0.3ex}I}}\_{com} \bar{\omega}$

Orbital angular momentum of a rigid body:
$\bar{L}\_{orb} = \bar{R}\_{com} \times \bar{P} = -M \bar{R}\_{com} \times \left( \bar{R}\_{com} \times \bar{\Omega} \right) = \bar{\bar{\kern{-0.3ex}I}}\_{orb} \bar{\Omega}$

[Kinetic energy of a rigid body](https://en.wikipedia.org/wiki/Moment_of_inertia#Kinetic_energy_2):
$T = P^2/(2 M) + \bar{\omega} \cdot \bar{L}\_{spn} / 2 = P_{rad}^2/(2 M) + L_{orb}^2/(2 M R^2) + \bar{\omega} \cdot \bar{L}\_{spn} / 2$

[Torque on a rigid body](https://en.wikipedia.org/wiki/Moment_of_inertia#Resultant_torque):
$\bar{\tau} = \bar{\tau}\_{orb} + \bar{\tau}\_{spn} = \bar{R}\_{com} \times \bar{F}\_{ext} + \left( \bar{\bar{\kern{-0.3ex}I}}\_{com} \bar{\alpha} + \bar{\omega} \cdot \bar{L}\_{spn} \right)$

[Precession of a spinning top](https://openstax.org/books/university-physics-volume-1/pages/11-4-precession-of-a-gyroscope):
$\Omega = M g R_{com} / L_{spn} = M g R_{com} / (I_{com} \omega) = 2 g R_{com} / (r^2 \omega)$

[Total energy](https://www.feynmanlectures.caltech.edu/I_13.html):
$E = \sum E_i = \sum_i \left( T_i + \sum_{j<i} U_{ij} \right), \space dE = dW_{non} = d\bar{r} \cdot \bar{F}\_{non}$

[Conservative force (field)](https://en.wikipedia.org/wiki/Conservative_force):
$\nabla \times \bar{F} = 0 \implies \bar{F} = -\nabla U, \space W = U_0 - U_1, \space E_0 = E_1$

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
$\bar{F} = -\mu N \hat{v}$, where $N$ is the sum of all forces directed towards the surface

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

$\bar{v} = d\bar{r} / dt = \dot{r} \hat{r} + r \omega \hat{\varphi} = \bar{v}\_{rad} + \bar{v}\_{tan}, \space \bar{L} = m r^2 \omega \left( \hat{r} \times \hat{\varphi} \right)$

$\bar{a} = d\bar{v} / dt = \left( \ddot{r} - r \omega^2 \right) \hat{r} + \left( 2 \dot{r} \omega + r \dot{\omega} \right) \hat{\varphi} = \bar{a}\_{rad} + \bar{a}\_{tan}$

$\bar{F} = m \bar{a} = \left( m \ddot{r} - m r \omega^2 \right) \hat{r} + \left( 2 m \dot{r} \omega + m r \dot{\omega} \right) \hat{\varphi} = \bar{F}\_{rad} + \bar{F}\_{tan}, \space \bar{\tau} = \left( 2 m r \dot{r} \omega + m r^2 \dot{\omega} \right) \left( \hat{r} \times \hat{\varphi} \right)$

$\bar{F} + m r \omega^2 \hat{r} - 2 m \dot{r} \omega \hat{\varphi} = m \tilde{a} = m \ddot{r} \hat{r} + m r \ddot{\varphi} \hat{\varphi}$

$\bar{F}\_{cen} = m r \omega^2 \hat{r}, \space \bar{F}\_{cor} = - 2 m \dot{r} \omega \hat{\varphi}$

[Levi-Civita symbol](https://en.wikipedia.org/wiki/Levi-Civita_symbol): $\varepsilon_{ijk} \varepsilon_{ipq} = \delta_{jp} \delta_{kq} - \delta_{jq} \delta_{kp}$

[Inner product](https://en.wikipedia.org/wiki/Dot_product): $\bar{a} \cdot \bar{b} = \bar{a}^{\mathsf{T}} \bar{b} = \delta_{ij} a_i b_j = a_i b_i$

[Outer product](https://en.wikipedia.org/wiki/Outer_product): $\left( \bar{b} \otimes \bar{c} \right)\_{jk} = \left( \bar{b} \bar{c}^{\mathsf{T}} \right)\_{jk} = b_j c_k$

[Cross product](https://en.wikipedia.org/wiki/Cross_product): $\left( \bar{b} \times \bar{c} \right)\_i = \varepsilon_{ijk} b_j c_k = \frac{1}{2} \varepsilon_{ijk} T_{jk}, \space T_{jk} = \varepsilon_{ijk} \left( \bar{b} \times \bar{c} \right)\_i = b_j c_k - b_k c_j, \space \bar{\bar{\kern{-0.3ex}T}} = \bar{b} \otimes \bar{c} - \left( \bar{b} \otimes \bar{c} \right)^{\mathsf{T}}$ is an antisymmetric tensor

[Scalar triple product](https://en.wikipedia.org/wiki/Triple_product): $\bar{a} \cdot \left( \bar{b} \times \bar{c} \right) = \mathrm{det}\negmedspace\left[ \bar{a} \space \bar{b} \space \bar{c} \right] = \varepsilon_{ijk} a_i b_j c_k$

[Vector triple product](https://en.wikipedia.org/wiki/Triple_product): $\bar{a} \times \left( \bar{b} \times \bar{c} \right) = \bar{b} \left( \bar{a} \cdot \bar{c} \right) - \bar{c} \left( \bar{a} \cdot \bar{b} \right)$
