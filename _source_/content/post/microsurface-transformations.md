---
title: "On Microsurface Transformations"
date: 2024-01-11T13:38:14-08:00
draft: true
---

At EGSR 2022, Atanasov, Koylazov, Dimov, and Wilkie presented a paper titled [Microsurface Transformations](https://onlinelibrary.wiley.com/doi/abs/10.1111/cgf.14590), where the authors discuss linear transformations of microfacet distributions. Since I have been long interested in surface geometry, its representations, and the associated transformations (something I have previously explored in the context of [bump and normal mapping](/post/surface-gradient/)), this work immediately caught my attention. While the subject may seem obscure, the idea of parameterizing surface detail using a (spatially varying) matrix is immensely powerful. It also brings much-needed conceptual clarity, as it enables the processing of geometry at all scales using the framework of linear algebra. This is highly desirable if one wishes to model the appearance of deformed objects, with stretching and bending properly affecting fine surface detail (and, thus, its reflectance properties). Perhaps you can imagine other, less conventional applications of microsurface transformations. We shall explore one of them in this article.

<!--more-->

## Microscopic Introduction

Pick an object, perhaps one that sits on your desk or lives in your head. Focus on a tiny fragment of its surface located at the point $\bm{P}$. If the fragment is sufficiently small, or is sufficiently far away, it will appear effectively flat (but not necessarily smooth). Therefore, to a negligible degree of error, we may replace this fragment by a first-order approximation[^1] -- its projection onto the (averaged) tangent plane. In the literature, this projected surface fragment is referred to as the *macrosurface*. It is characterized by the area $A$ and the unit normal vector $\bm{n}$. Fixing two (not necessarily unit or orthogonal) tangent vectors $\bm{t_u}$ and $\bm{t_v}$ is sufficient to complete the parameterization of the macrosurface.

[^1]: In the sense of a Taylor series expansion.

In contrast, the surface fragment overlaid onto the macrosurface is called the *microsurface* $\mathbb{M}$[^2]. In the context of the microfacet theory, a microsurface is not modeled explicitly, but rather represented by a statistical model.

[^2]: We use the superscript 2 to indicate that a surface is a two-dimensional manifold.

Let $\bm{m}$ denote the unit normal vector of the microsurface. Since it may correspond to several distinct points on the microsurface[^3], we have to define[^4] $dA(\bm{m}) = D(\bm{m}) d\Omega(\bm{m})$ as the differential area of (the elements of) the microsurface perpendicular to $\bm{m}$, where $d\Omega$ is the differential solid angle centered on $\bm{m}$, and $D$ is the *microfacet normal distribution*[^5] (abbreviated as the *NDF*) associated with the microsurface. This function, along with the microfacet normals themselves, is typically restricted to the unit hemisphere $\mathbb{H^2}$ (with $\bm{n}$ serving as the zenith direction), which implies that $\mathbb{M^2}$ must be a heightfield. However, this restriction is not strictly necessary, and can be easily lifted, with the microfacet normals potentially covering the entire unit sphere $\mathbb{S^2}$.

[^3]: The surface position is a multivalued function of the normal, so the latter cannot be used to parameterize a surface.

[^4]: If the surface is convex, one can interpret $D$ as a Jacobian of the transformation from the surface to the unit hemisphere.

[^5]: Not to be confused with the Gaussian (a.k.a. normal) distribution in statistics.

A valid microsurface and, thus, a valid NDF, must obey the *projected area constraint*

$$ \tag{1}
\begin{aligned}
	&\bm{v} \cdot \thinspace \bm{n} A
	\cr = \space
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}} \bm{m}(\bm{p}) dA(\bm{p})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} dA(\bm{m})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) d\Omega(\bm{m})
\end{aligned}
$$

for all $\bm{v} \in \mathbb{H^2}$ (e.i. the observer is assumed to be located above the macrosurface), where $A$ is conventionally normalized to 1 to make $D$ a valid probability density function. Expressed this way, it is clear that the constraint is actually vectorial (e.i. coordinate-independent, basis-independent) in nature.

Eqn. 1 tells us several things. By substituting a constant NDF (or from its definition), we can see that $D$ is measured in units of area per solid angle. By choosing $\bm{v} = \bm{n}$, we can see that the area of the microsurface projected onto the macrosurface must be equal to $A$. In general, both projected areas must coincide in all directions.

One of the simplest valid microsurfaces is a box[^6] (with the flipped bottom face playing the role of the macrosurface). It is instructive to analyze its two-dimensional counterpart -- a rectangle.

[^6]: Using the tools of calculus, we can decompose an arbitrary surface into a (possibly infinite) number of (sufficiently small) boxes.

*Linearity* of Eqn. 1 allows us to consider individual elements of the microsurface separately, and then sum up the results. As we apply it to the box, observe that the signed projected areas of the opposite faces cancel each other, leaving just the top (that is, of course, equivalent to the bottom). Another consequence is that a linearly transformed box (a parallelepiped) still represents a valid combination of micro and macrosurfaces, but, of course, the projected areas (and, thus, the values of the NDF) do not remain the same.

Clearly, a microsurface does not have to be smooth; however, this introduces discontinuities in the NDF, which is typically undesirable, unless the goal is to model a flat surface.

Erasing any part of the box will cause a projected area mismatch for certain angles. This may tempt you to draw a conclusion that the constraint implies that the microsurface must be continuous, but that is not the case. The issue lies in *translation invariance* of Eqn. 1: the projected area of an object is independent of its location. This property may seem innocuous at first, but coupled with linearity of Eqn. 1, it leads to a disaster: you can freely translate different parts of the microsurface in different directions without affecting the value of the integral.

*Shadowing function: remember that equality of integrands does not follow from equality of integrals.*

---

**Double-check units of D and G (using e.g. Walter et al.). The units of their product must match the units of the BRDF.**