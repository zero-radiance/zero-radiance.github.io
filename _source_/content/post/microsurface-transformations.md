---
title: "On Microsurface Transformations"
date: 2024-01-11T13:38:14-08:00
draft: true
---

At EGSR 2022, Atanasov, Koylazov, Dimov, and Wilkie presented a paper titled [Microsurface Transformations](https://onlinelibrary.wiley.com/doi/abs/10.1111/cgf.14590), where the authors discuss linear transformations of microfacet distributions. Since I am interested in surface geometry, its representations, and the associated transformations (something I have previously explored in the context of [bump and normal mapping](/post/surface-gradient/)), this work immediately caught my attention. While the subject may seem obscure, the idea of representing surface detail with a (spatially varying) matrix is immensely powerful. It also brings much-needed conceptual clarity, for it enables the processing of geometry at all scales using the framework of linear algebra. This is highly desirable if one wishes to model the appearance of deformed objects, with stretching and bending properly affecting fine surface detail (and, thus, its reflectance properties). Perhaps you can imagine other, less conventional applications of microsurface transformations. We shall explore one of them at the end of this article (improve this sentence...).

<!--more-->

## Micro Intro

Pick an object, perhaps one that sits on your desk or lives in your head. Focus on a tiny fragment of its surface located at the point $\bm{p}$. If the fragment is sufficiently small, or is sufficiently far away, it should appear effectively flat (but not necessarily smooth). Therefore, to a negligible degree of error, we may replace this fragment by a first-order approximation[^1] -- its projection onto the (averaged) tangent plane. In the literature, this projected surface fragment is referred to as the *macrosurface*. It is characterized by the area $A$ and the unit normal vector $\bm{n}$. Fixing two (not necessarily unit or orthogonal) tangent vectors $\bm{t_u}$ and $\bm{t_v}$ is sufficient to complete the parameterization of the macrosurface.

[^1]: In the  sense of a Taylor series expansion.

In contrast, the surface fragment overlaid onto the macrosurface is called the *microsurface*. In the context of the microfacet theory, a microsurface is not modeled explicitly, but rather represented by a statistical model.

Let $\bm{m}$ denote the unit normal vector of the microsurface. Since it may correspond to several distinct points on the microsurface, we must define $dA\_{m} = D(\bm{m}) d\omega\_{m}$ as the area of the infinitesimal element of the microsurface aligned with $\bm{m}$, where $d\omega\_{m}$ is the infinitesimal solid angle associated with $\bm{m}$, and $D$ is the *normal distribution* associated with the microsurface. This function is defined on the unit upper hemisphere $\mathbb{H^2}$, and must obey the constraint

$$ \tag{1}
	\int_{\mathbb{H^2}} (\bm{v} \cdot \bm{m}) D(\bm{m}) d\omega\_{m} = (\bm{v} \cdot \bm{n}) A
$$

for all $\bm{v} \in \mathbb{H^2}$, where $A$ is conventionally normalized to 1 to make $D$ a valid probability density function.

Eqn. 1 tells us several things. By substituting a constant normal distribution, we can see that $D$ is measured in units of area per solid angle. By choosing $\bm{v} = \bm{n}$, we can see that the area of the microsurface projected onto the macrosurface must be equal to $A$. In general, both projected areas must coincide in all directions. This implies that the microsurface must be continuous, otherwise we could choose $\bm{v}$ aligned with the gap, violating the constraint imposed by Eqn. 1. The microsurface does not have to be smooth; however, this introduces discontinuities in the normal distribution, which is typically undesirable, unless your goal is to model a flat surface.

