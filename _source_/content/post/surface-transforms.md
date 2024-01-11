---
title: "On Microsurface Transformations"
date: 2024-01-11T13:38:14-08:00
draft: true
---

Let $\bm{m}$ and $\bm{n}$ denote the unit normal vectors of the microsurface and the macrosurface, respectively. Furthermore, let $dA\_{m} = D(\bm{m}) d\omega\_{m}$ be the area of the infinitesimal element of the microsurface aligned with $\bm{m}$, where $d\omega\_{m}$ is the solid angle associated with $\bm{m}$, and $D$ is the normal distribution associated with the microsurface. This function is defined on the unit hemisphere $\mathbb{H^2}$, and must obey the constraint

$$ \tag{1}
	\int_{\mathbb{H^2}} (\bm{v} \cdot \bm{m}) D(\bm{m}) d\omega\_{m} = (\bm{v} \cdot \bm{n}) A
$$

for all $\bm{v} \in \mathbb{H^2}$, where $A$ is the area of the macrosurface conventionally set to 1.

Eqn. 1 tells us several things. By substituting a constant normal distribution, we can see that $D$ is measured in units of area per solid angle. And by choosing $\bm{v} = \bm{n}$, we can see that the area of the microsurface projected onto the macrosurface must be equal to $A$. In general, both projected areas must coincide for all directions. This implies that the microsurface must be continuous, otherwise we could choose $\bm{v}$ aligned with the gap, violating the constraint imposed by Eqn. 1. The microsurface does not have to be smooth; however, this introduces discontinuities in $D$, and that is typically undesirable, unless our goal is to model a flat surface.

