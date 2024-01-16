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

In contrast, the surface fragment overlaid onto the macrosurface is called the *microsurface* $\mathbb{M}$[^2]. In the microfacet theory, a microsurface is not modeled explicitly, but rather represented by a statistical model.

[^2]: We use the superscript 2 to indicate that the surface is a two-dimensional manifold.

Let $\bm{m}$ denote the unit normal vector of the microsurface. Since it may correspond to several distinct points on the microsurface, we have to define[^4] $dA(\bm{m}) / A = D(\bm{m}) d\Omega(\bm{m})$ as the fraction of the area of the microsurface perpendicular to $\bm{m}$, where $d\Omega$ is the differential solid angle centered on $\bm{m}$, and $D$ is the *microfacet normal distribution*[^5] (abbreviated as the *NDF*) associated with the microsurface. This function, along with the microfacet normals themselves, is typically restricted to the unit hemisphere $\mathbb{H^2}$ (with $\bm{n}$ serving as the zenith direction), which implies that $\mathbb{M^2}$ must be a heightfield. However, this restriction is not strictly necessary, and we shall let the microfacet normals potentially cover the entire unit sphere $\mathbb{S^2}$.

[^4]: If the surface is convex, one can interpret $DA$ as a Jacobian of the transformation from the surface to the unit hemisphere.

[^5]: Not to be confused with the Gaussian (a.k.a. normal) distribution in statistics.

A valid microsurface and, thus, a valid NDF, must obey the *projected area* constraint

$$ \tag{1a}
\begin{aligned}
	&\bm{v} \cdot \thinspace \bm{n} A
	\cr = \space
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}} \bm{m}(\bm{p}) dA(\bm{p})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} dA(\bm{m})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) A d\Omega(\bm{m})
\end{aligned}
$$

for all $\bm{v} \in \mathbb{S^2}$. Expressed this way, it is clear that the constraint is vectorial (e.i. coordinate-independent, basis-independent) in nature:

$$ \tag{1b}
	\bm{n} = \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) d\Omega(\bm{m}).
$$

Eqn. 1 tells us a few things. Geometrically, it says that the projected areas of the microsurface and the macrosurface must must coincide for any given direction. Furthermore, by substituting a constant NDF (or from its definition), we can see that it is measured in units of reciprocal solid angle.

In the special case of $\bm{v} = \bm{n}$, we obtain

$$ \tag{1c}
	1 = \int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
$$

In order for $(\bm{n} \cdot \bm{m}) D(\bm{m})$ to be a valid probability density function, it must be non-negative for all $\bm{m}$. That is the case only if $\mathbb{M^2}$ is a heightfield.

One of the simplest valid microsurfaces is a box[^6] (with the flipped bottom face playing the role of the macrosurface). It is instructive to analyze its two-dimensional counterpart -- a rectangle.

[^6]: Using the tools of calculus, we can decompose an arbitrary surface into a (possibly infinite) number of (sufficiently small) boxes.

*Linearity* of Eqn. 1 allows us to consider individual elements of the microsurface separately, and then sum up the results. As we apply it to the box, observe that the signed projected areas of the opposite faces cancel each other, leaving just the top (that is, of course, equivalent to the bottom). Another consequence is that a linearly transformed box (a parallelepiped) still represents a valid combination of micro and macrosurfaces, but, of course, the projected areas (and, thus, the values of the NDF) do not remain the same.

Clearly, a microsurface does not have to be smooth; however, this introduces discontinuities in its NDF, which is undesirable, unless the goal is to model a flat surface.

Erasing any part of the box will cause a projected area mismatch for certain angles. This may tempt you to draw the conclusion that the constraint implies that the microsurface must be continuous, but that is not the case. The issue lies in *translation invariance* of Eqn. 1, which simply means that the projected area of an object is independent of its location. This property may seem innocuous at first, but coupled with linearity of Eqn. 1, it leads to a disaster: you can freely translate different parts of the microsurface in different directions without affecting the value of the integral.

In addition to the ordinary projected area, we would like to calculate the *visible projected area* of the microsurface.

$$ \tag{2}
\begin{aligned}
	&\int_{\bm{p} \in \mathbb{M^2}}
	\mathrm{max}\big(0, \bm{v} \cdot \bm{m}(\bm{p})\big) V(\bm{p}, \bm{v}) dA(\bm{p})
	\cr = \space
	&\int_{\bm{m} \in \mathbb{S^2}}
	\mathrm{max}\big(0, \bm{v} \cdot \bm{m}(\bm{p})\big) G_1(\bm{m}, \bm{v}) dA(\bm{m})
	\cr = \space
	&\int_{\bm{m} \in \mathbb{S^2}}
	\mathrm{max}\big(0, \bm{v} \cdot \bm{m}(\bm{p})\big) G_1(\bm{m}, \bm{v}) D(\bm{m}) d\Omega(\bm{m}),
\end{aligned}
$$

where $V$ is the binary *visibility function* that evaluates to 0 if $\bm{p}$ is occluded along $\bm{v}$, and 1 otherwise, and $G_1$ is the dimensionless *masking function* that ... the fraction of the unoccluded differential area $dA(\bm{m})$ of the the microsurface. max(...)

Eqn. 1 and 2 are intimately related. For a valid (e.i. continuous) microsurface, if we choose $\bm{v} = \bm{n}$, the values if the integrals are the same. In general, and exemplified by a box (for a box, it fails), *the visible projected area is greater or equal to the ordinary projected area*. They coincide, provided the viewing angle is sufficiently steep, or the microgeometry -- sufficiently short, so that it is completely contained within the infinite volume spanned by sweeping the macrosuface along the viewing direction.

The microsurface must be infinitesimally tall.

*Shadowing function: remember that equality of integrands does not follow from equality of integrals.*
