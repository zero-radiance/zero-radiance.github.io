---
title: "On Microsurface Transformations"
date: 2024-01-11T13:38:14-08:00
draft: true
---

At EGSR 2022, Atanasov, Koylazov, Dimov, and Wilkie presented a paper titled [Microsurface Transformations](https://onlinelibrary.wiley.com/doi/abs/10.1111/cgf.14590), where the authors discuss linear transformations of microfacet distributions. Since I have been long interested in surface geometry, its representations, and the associated transformations (something I have previously explored in the context of [bump and normal mapping](/post/surface-gradient/)), this work immediately caught my attention. While the subject may seem obscure, the idea of parameterizing surface detail using a (spatially varying) matrix is immensely powerful. It also brings much-needed conceptual clarity, as it enables the processing of geometry at all scales using the framework of linear algebra. This is highly desirable if one wishes to model the appearance of deformed objects, with stretching and bending properly affecting fine surface detail (and, thus, its reflectance properties). Perhaps you can imagine other, less conventional applications of microsurface transformations. We shall explore one of them in this article.

<!--more-->

## Microscopic Introduction

Pick an object, perhaps one that sits on your desk or lives in your head. Focus on a tiny fragment of its surface located at the point $\small \bm{P}$. If the fragment is sufficiently small, or is sufficiently far away, it will appear effectively flat (but not necessarily smooth). Therefore, to a negligible degree of error, the fragment may be replaced by a first-order approximation[^1] -- its projection onto the (averaged) tangent plane. In the literature, this projected surface fragment is referred to as the *macrosurface*. It is characterized by the area $\small A$ and the unit normal vector $\small \bm{n}$. Fixing two (not necessarily unit or orthogonal) tangent vectors $\small \bm{t_u}$ and $\small \bm{t_v}$ is sufficient to complete the parameterization of the macrosurface.

[^1]: In the sense of a Taylor series expansion.

In contrast, the surface fragment overlaid onto the macrosurface is called the *microsurface* $\small \mathbb{M}$[^2]. In the microfacet theory, a microsurface is not modeled explicitly, but rather represented by a statistical model.

[^2]: We use the superscript 2 to indicate that the surface is a two-dimensional manifold.

Let $\small \bm{m}$ denote the unit normal vector of the microsurface. Since it may correspond to several distinct points on the microsurface, it is necessary to define[^3] $\small dA(\bm{m}) = D(\bm{m}) A d\Omega(\bm{m})$ as the differential area of a portion of the microsurface perpendicular to $\small \bm{m}$, where $\small d\Omega$ denotes the differential solid angle centered on $\small \bm{m}$, and $\small D$ is the *distribution of normals*[^4] (abbreviated as the *NDF*) associated with the microsurface. This function, along with the microfacet normals themselves, is typically restricted to the unit hemisphere $\small \mathbb{H^2}$ (with $\small \bm{n}$ serving as the zenith direction), which implies that $\small \mathbb{M^2}$ must be a heightfield. However, this restriction is not strictly necessary; we shall demonstrate that by letting the microfacet normals potentially cover the entire unit sphere $\small \mathbb{S^2}$.

[^3]: If the surface is convex, one can interpret $\small DA$ as a Jacobian of the transformation from the surface to the unit hemisphere.

[^4]: Not to be confused with the Gaussian (a.k.a. normal) distribution in statistics.

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

for all $\small \bm{v}$. Expressed this way, it is clear that the constraint is geometric (e.i. coordinate-independent, basis-independent) in nature:

$$ \tag{1b}
	\bm{n} = \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) d\Omega(\bm{m}).
$$

Eqn. 1 tells us a few things. Geometrically, it says that the projected areas of the microsurface and the macrosurface must must coincide in any given direction. Furthermore, by substituting a constant NDF (or from its definition), we can see that it is measured in units of reciprocal solid angle.

In the special case of $\small \bm{v} = \bm{n}$, we obtain

$$ \tag{1c}
	1 = \int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
$$

In order for $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$ to be a valid probability density function, it must be non-negative for all $\small \bm{m}$. That is the case only if $\small \mathbb{M^2}$ is a heightfield.

One of the simplest valid microsurfaces is a box[^5] (with the flipped bottom face playing the role of the macrosurface). It is instructive to analyze its two-dimensional counterpart -- a rectangle.

[^5]: Using the tools of calculus, we can decompose an arbitrary surface into a (possibly infinite) number of (sufficiently small) boxes.

*Linearity* of Eqn. 1 allows us to consider individual elements of the microsurface separately, and simply sum up the results. As we apply Eqn. 1 to the box, observe that the signed projected areas of the opposite faces cancel each other, leaving just the top (that is, of course, equivalent to the bottom). Another consequence is that a linearly transformed surface (a parallelepiped, in the case of a box) still results in a valid combination of the microsurface and the macrosurface. We can see why that is the case by picturing Eqn. 1 geometrically and (inversely) transforming the coordinate axes rather than the surface itself. Of course, while the surface lines are unchanged, the projected areas (and, thus, the values of the NDF) do not remain the same.

Clearly, a microsurface does not have to be smooth; however, this introduces discontinuities in its NDF, which is undesirable, unless the goal is to model a flat surface.

Erasing any part of the box will cause a projected area mismatch for certain angles. This may tempt you to draw a conclusion that the constraint implies that the microsurface must be continuous, but that is not the case. The issue lies in *translation invariance* of Eqn. 1, which simply means that the projected area of an object is independent of its location. This property may seem innocuous at first, but coupled with linearity of Eqn. 1, it spells disaster: you can freely translate different parts of the microsurface in different directions without affecting the value of the integral.

Since the projected area is insufficient to describe a real surface, we may additionally specify its *visible projected area*

$$ \tag{2a}
\begin{aligned}
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) dA(\bm{m})
	\cr = \space
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) D(\bm{m}) A d\Omega(\bm{m})
\end{aligned}
$$

in terms of the dimensionless *masking function* $\small G_1(\bm{v}, \bm{m})$ that gives the fraction of the differential area $\small dA(\bm{m})$ of a portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be unoccluded along $\small \bm{v}$. It is closely related to the binary *visibility function* $\small V(\bm{v}, \bm{p})$ that outputs 0 if the point $\small \bm{p}$ is occluded along $\small \bm{v}$, and 1 otherwise. Both functions take *self-masking* into account: $\small V = G_1 = 0$ if $\small (\bm{v} \cdot \bm{m}) \le 0$. This subtle point allows us to emphasize the geometric (e.i. coordinate-independent, basis-independent) nature of Eqn. 2.

The masking (and the visibility) function possesses an important property called *stretch invariance*, or, more generally, *invariance under linear transformations*. We have already seen that, in the context of the microfacet theory, a linearly transformed surface remains valid; however, unlike the NDF, the masking function is dimensionless: it only encodes visibility, and so it remains unaffected by a transformation of the coordinate axes. Of course, such a of transformation affects everything, including the view direction.

Eqn. 1 and 2 are closely related. For a valid microsurface, the values of the integrals are the same[^6] if we choose $\small \bm{v} = \bm{n}$:

[^6]: Remember that the equality of integrands does not necessarily follow from the equality of integrals.

$$ \tag{2b}
\begin{aligned}
	1 =
	&\int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})
	\cr =
	&\int_{\bm{m} \in \mathbb{S^2}}
	(\bm{n} \cdot \bm{m}) G_1(\bm{n}, \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
\end{aligned}
$$

As clearly demonstrated by the box, in general, *the visible projected area is greater or equal to the ordinary projected area*. This is caused by self-masking, which eliminates the (formerly negative) contribution of back-facing surface elements. The two types of projected areas coincide only if the view angle is sufficiently steep, or the microgeometry -- sufficiently short, so that the latter does not extend outside the infinite volume formed by sweeping the macrosuface along the view direction. This leads to one of the key assumptions of the microfacet theory: *the microsurface must be infinitesimally tall*. This limitation can manifest itself at grazing angles, and should be familiar to those who have some experience with bump and normal maps.

With the assumption in place, we can combine Eqn. 1a and 2a into

$$ \tag{3a}
	1 =
	\int_{\bm{m} \in \mathbb{S^2}} D_{vis}(\bm{v}, \bm{m}) d\Omega(\bm{m}),
$$

where

$$ \tag{3b}
	D_{vis}(\bm{v}, \bm{m}) =
	\frac{(\bm{v} \cdot \thinspace \bm{m})}{(\bm{v} \cdot \thinspace \bm{n})} G_1(\bm{v}, \bm{m}) D(\bm{m})
$$

is called the *distribution of visible normals*[^7] (abbreviated as the *VNDF*). Similarly to $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$, the former is a valid (view-dependent) probability density function only if $\small \mathbb{M^2}$ is a heightfield.

[^7]: After taking the definitions into account and comparing Eqn. 1c with 3a, it would be more natural to simply let $\small D_{vis} = G_1 D$. We stick with Eqn. 3b in order to conform to the existing body of literature.

The VNDF can be used to construct a *bidirectional scattering distribution function* $\small f_s$ (also known as a *BSDF*). By definition, it is a ratio of the differential outgoing radiance to the differential incident irradiance, the latter being the product of the incident radiance and the projected differential solid angle $\small d\Omega_n(\bm{l}) = \vert \bm{l} \cdot \bm{n} \vert d\Omega(\bm{l})$:

$$ \tag{4a}
	f_s(\bm{v}, \bm{n}, \bm{l}) =
	\frac{dL_o(\bm{v})}{dE_i(\bm{n}, \bm{l})} =
	\frac{dL_o(\bm{v})}{L_i(\bm{l}) d\Omega_n(\bm{l})}.
$$

In other words, the outgoing radiance is a weighted linear combination of the incident radiance:

$$ \tag{4b}
	L_o(\bm{v}) =
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{n}, \bm{l}) L_i(\bm{l}) d\Omega_n(\bm{l}).
$$

In order for the BSDF to be physically meaningful, it must satisfy three properties:

$$ \tag{4c}
\begin{aligned}
	\text{non-negativity: }
	&f_s \ge 0;
	\cr
	\text{reciprocity: }
	&\frac{f_s(\bm{v}, \bm{n}, \bm{l})}{f_s(\bm{l}, \bm{n}, \bm{v})} = \frac{\eta_v^2}{\eta_l^2};
	\cr
	\text{energy conservation: }
	&\int_{\bm{v} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{v}) \le 1;
\end{aligned}
$$

where $\small \eta_l$ and $\small \eta_v$ are the refractive indices associated with the directions of incidence $\small (\bm{l})$ and exitance $\small (\bm{v})$, respectively, that may point above or below the surface. The ratio is the consequence of Snell's law

$$ \tag{5}
	\eta_v \sin{\theta_v} = \eta_l \sin{\theta_l}
$$

that leads to the compression of solid angles and, thus, the increase in energy density.

We start with a concrete example of a perfectly smooth planar surface. Its BSDF is expressed in terms of the *Dirac delta function* $\small \delta$ defined as a solid angle measure by

$$ \tag{6a}
	\int_{\bm{v} \in \mathbb{S^2}} f(\bm{v}) \delta\big(d\Omega(\bm{v})\big) =
	\int_{\bm{v} \in \mathbb{S^2}} f(\bm{v}) \delta_{\Omega}(\bm{v}) d\Omega(\bm{v}) = f(\bm{0}).
$$

Then, the reflection component of the BSDF is

$$ \tag{6b}
	f_r(\bm{v}, \bm{n}, \bm{l}) =
	\delta_{\Omega}\negmedspace\left( \bm{n} - \frac{\bm{v} + \bm{l}}{\Vert \bm{v} + \bm{l} \Vert} \right) \frac{2 F}{(\bm{v} + \bm{l}) \cdot \bm{n}},
$$

where $\small 0 \le F \le 1$ is the *Fresnel reflectance*...

(6b clearly conforms to 4c)

Similarly, the transmission component is defined as

$$ \tag{6c}
	f_t(\bm{v}, \bm{n}, \bm{l}) =
	\delta_{\Omega}\big( \eta_v (\bm{v} \times \bm{n}) + \eta_l (\bm{l} \times \bm{n}) \big) \frac{1 - F}{(\bm{l} \cdot \bm{n})},
$$

(Does 6c conform to 4c? Demonstrate reciprocity and conservation for dielectrics)

---

TODO: ACTIVE AND PASSIVE TRANSFORMATIONS

---

## Acknowledgements

## References and Suggestions for Further Reading

Veach's thesis

Eric's understanding

Walter's ggx

Atanasov's transformations