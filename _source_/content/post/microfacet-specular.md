---
title: "Construction of a Microfacet Specular BSDF: A Geometric Approach"
date: 2024-05-20
tags: [
    "Microfacet",
    "Specular",
    "BSDF",
    ]
---

The microfacet theory is one of the core components of physically based rendering. Unfortunately, it is often perceived as being rather complicated; primarily, for two reasons. The first one is that most papers only focus on a single aspect (such as the distribution of normals or the masking function), while neglecting the big picture. The second problem is the lack of geometrical interpretations of the mathematical expressions, which inhibits understanding at the intuitive level.

A solid grasp of the fundamentals of the theory is an essential requirement for working on its extensions or applications (such as Smith's approximation, multiple scattering, bump roughness, microsurface transformations, and so on). This author aims to address these issues by collecting the fragments from the literature and explaining how they fit together.

<!--more-->

### Microsurface and Macrosurface

Pick an object in front of you, and focus on a tiny fragment of its surface. If this fragment is sufficiently small, or is sufficiently far away, it will look effectively flat (but not necessarily smooth). Therefore, to a negligible degree of error, the fragment may be replaced by the first-order approximation[^1] -- its orthogonal projection onto the averaged tangent plane. In the literature, this projected surface is referred to as the *macrosurface* (see Fig. 1 below). It is characterized by the *area* $\small A$ and the *unit normal vector*[^6] $\small \bm{n}$. Fixing the origin and two (linearly independent, but not necessarily unit or orthogonal) tangent vectors is sufficient to complete the parameterization of the macrosurface.

[^1]: In the sense of a Taylor series expansion.

[^6]: We shall treat all surfaces as one-sided unless stated otherwise.

{{< figure src="/img/micro/micro_macro.svg" caption="*Figure 1. Microsurface (red) and macrosurface (green).*" >}}

In contrast, the surface fragment overlaid onto the macrosurface is called[^2] the *microsurface* $\small \mathbb{M^2}$. It is composed of infinitesimal elements called *microfacets*. In the microfacet theory, a microsurface is typically not modeled explicitly, but represented by a statistical model.

[^2]: We use the superscript 2 to indicate that a surface is a two-dimensional manifold.

### Distribution of Normals

Let $\small \bm{m}$ denote a unit normal vector of the microsurface. Since it may correspond to several distinct microfacets, we must define $\small dA(\bm{m}) = D(\bm{m}) A d\Omega(\bm{m})$ as the *differential area* of the portion of the microsurface perpendicular to $\small \bm{m}$, where $\small d\Omega$ is the *differential solid angle* centered on $\small \bm{m}$, and $\small D$ is the *distribution of normals*[^4] (abbreviated as the NDF) associated with the microsurface[^3]. The domain of this distribution (the set of the microsurface normals) is typically restricted to the *unit hemisphere* $\small \mathbb{H^2}$ (with $\small \bm{n}$ pointing at the zenith), which requires $\small \mathbb{M^2}$ to be a height field. However, this restriction is not strictly necessary; we shall demonstrate this by letting the microsurface normals (potentially) cover the entire *unit sphere* $\small \mathbb{S^2}$.

[^3]: If the microsurface is convex, we can interpret $\small DA = dA/d\Omega$ as the Jacobian of the transformation from the unit sphere to the surface area.

[^4]: Not to be confused with the normal (a.k.a. Gaussian) distribution from the probability theory.

A valid microsurface and, thus, a valid NDF, must obey the *signed (orthogonally) projected area* constraint

$$ \tag{1a}
\begin{aligned}
	&\bm{v} \cdot \bm{n} A
	\cr =
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}} \bm{m}(\bm{p}) dA(\bm{p})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} dA(\bm{m})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) A d\Omega(\bm{m})
\end{aligned}
$$

for every vector $\small \bm{v}$. Expressed this way, it is clear that the constraint is geometric (coordinate-independent, basis-independent) in nature:

$$ \tag{1b}
	\bm{n} = \int_{\bm{m} \in \mathbb{S^2}} \bm{m} D(\bm{m}) d\Omega(\bm{m}).
$$

Eqn. 1b is the definition of the macrosurface normal $\small \bm{n}$ for a given microsurface. Furthermore, substitution $\small \bm{v} = \bm{n}$ in Eqn. 1a yields the macrosurface area $\small A$.

Eqn. 1a tells us a few things. First, it says that the signed projected areas of the microsurface and the macrosurface must coincide in any given direction. Furthermore, by substituting a constant NDF (or, from the definition), we can see that an NDF is measured in units of reciprocal solid angle.

In the special case of $\small \bm{v} = \bm{n}$, the integrand is normalized:

$$ \tag{1c}
	1 = \int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
$$

In order for $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$ to be a valid probability density function, it must be non-negative for all $\small \bm{m}$. By definition, it is always the case that $D \ge 0$, while $\small (\bm{n} \cdot \bm{m}) \ge 0$ if $\small \mathbb{M^2}$ is a height field.

{{< figure src="/img/micro/rectangle.svg" caption="*Figure 2. Rectangular microsurface (red) and macrosurface (green).*" >}}

One of the simplest examples of a valid microsurface is a box[^5], with the flipped bottom face playing the role of the macrosurface. It is instructive to analyze its two-dimensional counterpart -- a rectangle (see Fig. 2 above).

[^5]: Using the tools of calculus, we can decompose an arbitrary surface into a (potentially infinite) number of sufficiently small boxes.

This trivial case clearly demonstrates that a microsurface does not have to be smooth (continuously differentiable); however, since that ensures the continuity of the NDF, it is desirable, unless the goal is to model a flat surface.

The *linearity* of Eqn. 1a allows us to consider the individual microfacets separately and to sum up their contributions. As you apply it to the box along some $\small \bm{v}$, observe that the signed projected areas of the sides cancel each other, while the top remains equivalent to the bottom. Another consequence is that an application of a *linear transformation* (which transforms a box into a parallelepiped, in this case) also generates a valid combination of the microsurface and the macrosurface. We can see why that is the case by interpreting Eqn. 1a geometrically and (passively) transforming the coordinate axes[^51] rather than (actively transforming) the surface itself (see Fig. 3 below). Of course, while the surface lines remain unchanged, the signed projected areas (and, thus, the values of the NDF) do not stay the same.

[^51]: This requires the transformation to be invertible. And if the transformation were nonlinear, the transformed coordinate axes would vary from point to point, forming vector fields.

{{< figure src="/img/micro/transformed.svg" caption="*Figure 3. Passive (left) vs active (right) transformation.*" >}}

Erasing any part of the box leads to a signed projected area mismatch for certain angles. Therefore, the constraint may seem to imply that the microsurface must be continuous, but, unfortunately, that is not the case. The issue lies in the *translation invariance* of Eqn. 1a; specifically, it means that the orthogonally projected area of an object is independent of its location. This property may seem innocuous at first, but, coupled with the linearity, it spells disaster: we may freely translate different microfacets in different directions without affecting the value of the integral.

### Masking Function

Since the signed projected area is an incomplete description of a real surface (as opposed to a microfacet soup or salad[^52]), we may additionally specify its *visible (orthogonally) projected area*

[^52]: In technical terms, such a surface has a very short auto-correlation distance.

$$ \tag{2a}
\begin{aligned}
	&\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) dA(\bm{m})
	\cr =
	&\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_1(\bm{v}, \bm{m}) D(\bm{m}) A d\Omega(\bm{m}),
\end{aligned}
$$

which depends on the relative positions of the microfacets (see Fig. 4 below). It is defined in terms of the dimensionless *masking function* $\small G_1(\bm{v}, \bm{m})$ that gives the fraction of the differential area $\small dA(\bm{m})$ of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along $\small \bm{v}$. In other words, it is the *average visibility* (along the view vector $\small \bm{v}$) of the microfacets with the normal $\small \bm{m}$. The masking function is closely related to the binary *visibility function* $\small V(\bm{v}, \bm{p})$ that outputs 0 if the point $\small \bm{p}$ is occluded along $\small \bm{v}$, and 1 otherwise. Both functions take *self-occlusion* into account: $\small V = G_1 = 0$ if $\small (\bm{v} \cdot \bm{m}) \le 0$.

{{< figure src="/img/micro/visibility.svg" caption="*Figure 4. Visible, front-facing area (red) and occluded, back-facing area (blue) vs signed area (green).*" >}}

The masking and visibility functions possess an important property called *stretch invariance*, or, more generally, *invariance under linear transformations*. We have already seen that, in the context of the microfacet theory, a linearly transformed surface remains valid; however, unlike an NDF, a masking function is dimensionless: it only encodes visibility, and so it remains unaffected by a transformation of the coordinate axes. Of course, a passive transformation must be applied to everything, including the vector $\small \bm{v}$.

### Inherent Assumptions

Eqn. 1 and 2 are connected. For a valid microsurface, the values of the integrals are the same if $\small \bm{v} = \bm{n}$:

$$ \tag{2b}
\begin{aligned}
	1 =
	&\int_{\bm{m} \in \mathbb{S^2}} (\bm{n} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})
	\cr =
	&\int_{\bm{m} \in \mathbb{S^2}}
	(\bm{n} \cdot \bm{m}) G_1(\bm{n}, \bm{m}) D(\bm{m}) d\Omega(\bm{m}).
\end{aligned}
$$

Eqn. 2b can be visualized as the microsurface collapsing onto the macrosurface, with the downward-facing microfacets canceling or occluding those below them. In the special case of a height field, $\small G_1(\bm{n}, \bm{m}) = 1$.

{{< figure src="/img/micro/comparison.svg" caption="*Figure 5. Visible, front-facing area (red) and occluded, back-facing area (blue) vs signed area (green).*" >}}

For a continuous surface, *the visible projected area is greater or equal to the signed projected area*. This inequality stems from self-occlusion, which eliminates the (otherwise negative) contribution of back-facing microfacets. The two types of projected areas coincide if and only if the view angle is sufficiently steep, or the microsurface -- sufficiently short, so that the latter does not extend outside the volume swept by the macrosuface translated along the view vector. The difference of the areas can be reduced by flattening or tiling the microsurface (see Fig. 5 above and Fig. 6 below); both methods effectively reduce its height relative to the horizontal dimensions.

{{< figure src="/img/micro/tiling.svg" caption="*Figure 6. Visible area (red) and occluded area (blue) vs signed area (green). The entire visible area is front-facing; not all of the occluded area is back-facing.*" >}}

Of course, as the view angle (measured w.r.t. the macrosurface normal) tends to 90 degrees, the signed projected area shrinks to zero, while the visible projected area becomes proportional to the height of the microsurface. This leads to one of the key assumptions of the microfacet theory: *a microsurface is infinitesimally tall*. This limitation is particularly apparent at grazing angles, and should be familiar to those familiar with bump and normal mapping.

If we want to posit that Eqn. 1a and 2a are equivalent, we must also prevent the signed projected area from turning negative by restricting the view angles to the range where $\small (\bm{v} \cdot \bm{n}) > 0$. In other words, *the observer is located above the surface*. Otherwise, we must obtain and utilize the statistics for the undersurface.

We do not have to start from scratch. For instance, it is easy to see that reversing the directions of both $\small \bm{n}$ and $\small \bm{m}$ allows us to reuse the original distribution of normals. As for the view vector $\small \bm{v}$, we can either reverse its direction or reflect it across the macrosurface. Since flipping a surface upside down alters visibility, neither method yields exact results; however, the former is preferable, since it guarantees that self-occlusion is properly accounted for. The resulting approximation is reasonably convincing for complex unstructured surfaces; however, you should not expect it to be valid for a simple repeating pattern.

### Distribution of Visible Normals

With the assumption (that the projected surface areas are the same) in place, we can combine Eqn. 1a and 2a into

$$ \tag{3a}
	1 =
	\int_{\bm{m} \in \mathbb{S^2}} D_{vis}(\bm{v}, \bm{n}, \bm{m}) d\Omega(\bm{m}),
$$

where

$$ \tag{3b}
	D_{vis}(\bm{v}, \bm{n}, \bm{m}) =
	\frac{(\bm{v} \cdot \bm{m})}{(\bm{v} \cdot \bm{n})} G_1(\bm{v}, \bm{m}) D(\bm{m})
$$

is called the *distribution of visible normals*[^7] (abbreviated as the VNDF). Its meaning becomes clear in the context of Eqn. 2a: $\small ((\bm{v} \cdot \bm{m})/(\bm{v} \cdot \bm{n}))^{-1} D_{vis}(\bm{v}, \bm{n}, \bm{m}) A d\Omega(\bm{m})$ is the differential area of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along $\small \bm{v}$. Like $\small (\bm{n} \cdot \bm{m}) D(\bm{m})$, the VNDF is a probability density function; but, due to self-occlusion, this property extends beyond height fields.

[^7]: After comparing Eqn. 1c with 3a and taking the definitions into account, it seems more natural to define $\small D_{vis} = G_1 D$. Nevertheless, we stick with Eqn. 3b to conform to the existing body of literature.

<!--

### Smith's Approximation of the Masking Function (to be omitted)

In practice, one usually neither creates nor is given an actual microsurface, but rather specifies the statistical distributions directly. In fact, one generally goes one step further: since the NDF and the masking function are not independent (they satisfy Eqn. 3), a reasonable course of action is to specify just one and attempt to derive the other. Which distribution should we choose as the starting point? The answer comes from the definitions: $\small G_1$ is built on top of $\small D$, and the role of the former is to endow the latter with visibility data.

Thus, one typically starts with the NDF. Unfortunately, as we have already seen, it does not provide a complete surface description. This forces us to introduce additional assumptions in the form of a *microsurface profile*. Arguably, the simplest assumption is the *normal-occlusion independence* introduced by Smith:

$$ \tag{3Xa}
	G_1^S(\bm{v}, \bm{m}) = \Theta(\bm{v} \cdot \bm{m}) W_1(\bm{v}),
$$

where

$$ \tag{3Xb}
	\Theta(x) =
	\begin{cases}
	   1 &\text{if } x \ge 0, \cr
	   0 &\text{otherwise},
	\end{cases}
$$

is the *Heaviside step function*, which is used to model self-occlusion. As the name implies, the remaining occlusion term $\small W_1$ that models the average occlusion is independent of the microsurface normal $\small \bm{m}$.

In reality, the way the visibility depends on the surface normal is more complicated. Take a height field as an example: for shallow view angles, vertical microfacets are more likely to be occluded compared to horizontal ones. Fortunately, the issue is not very apparent unless one examines a rough surface at a grazing angle, where, as we have previously remarked, the foundations of the microfacet theory itself are on shaky ground.

Substitution of Eqn. 3X into 3 yields a ratio of projected areas (signed to front-facing; see Fig. 7 below):

$$ \tag{3Xc}
\begin{aligned}
	W_1(\bm{v})
	&= \frac{(\bm{v} \cdot \bm{n})}{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})}
	\cr
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} (\bm{v} \cdot \bm{m}) dA(\bm{m})}{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m})}
	\cr
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} (\bm{v} \cdot \bm{m}) dA(\bm{m})}{\int_{\bm{m} \in \mathbb{S^2}} (\bm{v} \cdot \bm{m}) dA(\bm{m}) + \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, -\bm{v} \cdot \bm{m}) dA(\bm{m})}
	\cr
	&= \frac{1}{1 + \Lambda(\bm{v})},
\end{aligned}
$$

where

$$ \tag{3Xd}
\begin{aligned}
	\Lambda(\bm{v})
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, -\bm{v} \cdot \bm{m}) dA(\bm{m})}{(\bm{v} \cdot \bm{n}) A}
	\cr
	&= \frac{\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, -\bm{v} \cdot \bm{m}) D(\bm{m}) d\Omega(\bm{m})}{(\bm{v} \cdot \bm{n})}
\end{aligned}
$$

is another ratio of projected areas (back-facing to signed; see Fig. 7 below). For a height field, $\small \Lambda(\bm{n}) = 0$.

{{< figure src="/img/mst/fig7.png" >}}

Eqn. 3Xc confirms that the connection between a microfacet and its neighborhood is absent. This puts us at risk of ending up with a microfacet soup or a salad (instead of a continuous surface) again. The only way to remedy this deficiency is by using a more sophisticated microsurface profile.

**Q: How accurate is Smith's approximation of the masking function?**

-->

### General BSDF

A VNDF can be used to construct a *bidirectional scattering distribution function* $\small f_s$. By definition, a BSDF is a ratio of the differential [outgoing radiance](/post/particle-volume/#radiometry-crash-course) to the differential [incident irradiance](/post/particle-volume/#radiometry-crash-course), the latter being the product of the incident radiance and the *projected differential solid angle* $\small d\Omega_n(\bm{l}) = \vert \bm{n} \cdot \bm{l} \vert d\Omega(\bm{l})$:

$$ \tag{4a}
	f_s(\bm{v}, \bm{n}, \bm{l}) =
	\frac{dL(\bm{v})}{dE_n(\bm{l})} =
	\frac{dL(\bm{v})}{L(\bm{l}) d\Omega_n(\bm{l})}.
$$

In other words, the outgoing radiance is a weighted average of the incident radiance over the surface area of the unit sphere (or, equivalently, the double-sided unit disk):

$$ \tag{4b}
	L(\bm{v}) =
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{n}, \bm{l}) L(\bm{l}) d\Omega_n(\bm{l}).
$$

In order for a BSDF to be physically meaningful, it must satisfy three requirements[^8]:

[^8]: The total energy of the system (comprised of the photon and the surface) is always conserved. The kinetic energy of the photon is only conserved if the integral is equal to 1. Otherwise, by definition, the collision is called inelastic.

$$ \tag{4c}
\begin{aligned}
	\textit{non-negativity: }
	&f_s \ge 0;
	\cr
	\textit{reciprocity: }
	&\frac{f_s(\bm{v}, \bm{n}, \bm{l})}{f_s(\bm{l}, \bm{n}, \bm{v})} = \frac{\eta_v^2}{\eta_l^2};
	\cr
	\textit{conservation of energy: }
	&\text{for all } \boldsymbol{l} \text{,} \int_{\bm{v} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{v}) \le 1;
\end{aligned}
$$

where $\small \eta_v$ and $\small \eta_l$ are the real[^9] *indices of refraction* (abbreviated as the IOR) associated with the directions of exitance (along the view vector $\small \bm{v}$) and incidence (along the light vector $\small \bm{l}$), respectively. In particular, reciprocity is a consequence of the *law of refraction* (also valid for reflection)

[^9]: We will not consider absorptive or magnetic media in this article.

$$ \tag{5a}
	\eta_v \Vert \bm{v} \times \bm{n} \Vert = \eta_l \Vert \bm{l} \times \bm{n} \Vert.
$$

Differentiation of Eqn. 5a leads to the *compression of projected solid angles*

$$ \tag{5b}
	\eta_v^2 d\Omega_n(\bm{v}) = \eta_l^2 d\Omega_n(\bm{l}),
$$

and, thus, a change in energy density (provided $\small \eta_v \neq \eta_l$). Note that the relationship between the ordinary solid angles is not quite as simple:

$$ \tag{5c}
	\eta_v^2 d\Omega(\bm{v}) \neq \eta_l^2 d\Omega(\bm{l}).
$$

Eqn. 5b provides a strong motivation to utilize projected solid angles. Failure to do so consistently often results in complicated expressions or subtle errors.

The properties of a valid BSDF are by no means obvious. In particular, ensuring reciprocity is a non-trivial task. A typical approach represents the BSDF as a sum of two components: reflection (the BRDF $\small f_r$) and transmission (the BTDF $\small f_t$), with the direction of exitance (along the view vector $\small \bm{v}$) corresponding to either the reflection vector $\small \bm{r}$ or the refraction vector $\small \bm{t}$. The principal advantage of this method lies in the fact that the reflection component is always *symmetric*: if $\small \bm{v}$ and $\small \bm{l}$ always point away from the surface, $\small \eta_v = \eta_l$ and, therefore, $\small f_r(\bm{v}, \bm{n}, \bm{l}) = f_r(\bm{l}, \bm{n}, \bm{v})$.

>> Appendix reflected refracted vector

### Smooth Specular BSDF

We shall illustrate these properties using a concrete example. Consider a perfectly smooth, planar surface. Its BSDF (sometimes referred to as the *smooth specular* BSDF) can be expressed in terms of the *Dirac delta "function"* $\small \delta\_{\mu}$ defined (with respect to the measure $\small \mu$) by the identity

$$ \tag{6a}
	f(\bm{k}) =
	\int \delta_{\mu}(\bm{k} - \bm{l}) f(\bm{l}) d{\mu}(\bm{l})
$$

valid for any function $\small f$ continuous at $\small \bm{k}$. Since we shall exclusively utilize the projected solid angle measure $\small \mu = \Omega\_n$, we may simply define

$$ \tag{6b}
	f(\bm{k}) =
	\int_{\bm{l} \in \mathbb{S^2}} \delta(\bm{k} - \bm{l}) f(\bm{l}) d\Omega_n(\bm{l})
$$

Note the similarity to Eqn. 4b.

The Dirac delta function possesses the following unsurprising property valid for a bijective function $\small \beta$:

$$ \tag{6c}
	\delta_{\mu}(\bm{k} - \bm{l}) d{\mu}(\bm{l}) =
	\delta_{\mu \circ \beta}\big(\beta(\bm{k}) - \beta(\bm{l})\big) d(\mu \circ \beta)(\bm{l}),
$$

where $\small (\mu \circ \beta)(\bm{l}) = \mu(\beta(\bm{l}))$. What it essentially means is that you must be consistent when transforming the vectors (or the coordinate axes).

Reflect and refract operators (already defined below)...

Now, according to the *law of reflection*, the light and reflection angles must be the same:

$$ \tag{7a}
	\sin{\theta_l} = \Vert \bm{l} \times \bm{n} \Vert = \Vert \bm{r} \times \bm{n} \Vert,
	\quad
	\cos{\theta_l} = \vert \bm{l} \cdot \bm{n} \vert = \vert \bm{r} \cdot \bm{n} \vert.
$$

On the other hand, we need to distinguish between the IORs associated with the exterior (above the surface) and the interior (below the surface) of the object:

$$ \tag{7b}
	\eta_t(\bm{l}) =
	\begin{cases}
	   \eta_{int} &\text{if } \eta_l = \eta_{ext}, \cr
	   \eta_{ext} &\text{otherwise}.
	\end{cases}
$$

Using this formalism and the law of reflection, the smooth specular BRDF can be expressed as

$$ \tag{7c}
	f_r(\bm{v}, \bm{n}, \bm{l}) =
	F(\theta_l, \eta_t(\bm{l})/\eta_l)
	\delta\big( \vert \bm{n} \cdot (\bm{v} + \bm{l}) \vert \bm{n} - (\bm{v} + \bm{l}) \big),
$$

where $\small 0 \le F \le 1$ is the dimensionless *Fresnel reflectance*

$$ \tag{8a}
	F(\theta_l, \eta_t/\eta_l)
	= (1 - \alpha) R_s(\theta_l, \eta_t/\eta_l) + \alpha R_p(\theta_l, \eta_t/\eta_l),
$$

and its mutually perpendicular components are defined as follows:

$$ \tag{8b}
\begin{aligned}
	R_s(\theta_l, \eta_t/\eta_l)
	&= \left| \frac{\cos{\theta_l} - (\eta_t / \eta_l) \cos{\theta_t}}{\cos{\theta_l} + (\eta_t / \eta_l) \cos{\theta_t}} \right|^2
	= \left| \frac{\sin(\theta_t - \theta_l)}{\sin(\theta_t + \theta_l)} \right|^2,
	\cr
	R_p(\theta_l, \eta_t/\eta_l)
	&= \left| \frac{\cos{\theta_t} - (\eta_t / \eta_l) \cos{\theta_l}}{\cos{\theta_t} + (\eta_t / \eta_l) \cos{\theta_l}} \right|^2
	= \left| \frac{\sin(2 \theta_t) - \sin(2 \theta_l)}{\sin(2 \theta_t) + \sin(2 \theta_l)} \right|^2.
\end{aligned}
$$

The value of $\small \alpha$ depends on the polarization state of the incident light: $\small \alpha = 0$ corresponds to the s-polarized light, $\small \alpha = 1$ -- to the p-polarized light, and $\small \alpha = 1/2$ -- to the unpolarized (natural) light.

The angle of refraction $\small \theta_t$ can be determined from the light angle $\small \theta_l$ and the *relative IOR* $\small \eta_t/\eta_l$ using the law of refraction and basic trigonometry:

$$ \tag{8c}
	\sin{\theta_t} = (\eta_t / \eta_l)^{-1} \sin{\theta_l},
	\quad
	\cos{\theta_t} = \sqrt{1 - \sin^2{\theta_t} }.
$$ 

The solution of the Fresnel equations is symmetric -- the value of $\small F$ is invariant under the exchange of the subscripts $\small l \text{ and } t$. Intuitively, the Fresnel reflectance (which is a ratio of two irradiance values) depends only on the path taken by light, and is unaffected by the reversal of its direction. In practice, this allows us to refract the view vector $\small \bm{v}$ (as opposed to the light vector $\small \bm{l}$), which yields an alternative formulation of the smooth specular BRDF:

$$ \tag{7c}
	f_r(\bm{v}, \bm{n}, \bm{l}) =
	F(\theta_v, \eta_t(\bm{v})/\eta_v)
	\delta\big( \vert \bm{n} \cdot (\bm{v} + \bm{l}) \vert \bm{n} - (\bm{v} + \bm{l}) \big).
$$

It is energy-conserving, which can be verified by substituting Eqn. 7c into 4c. In particular,

$$ \tag{7d}
	\int_{\bm{v} \in \mathbb{S^2}}
	f_r(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{v}) =
	F(\theta_l, \eta_t(\bm{l})/\eta_l) \le 1.
$$

$\small \eta_t/\eta_l \to 1$ leads to $\small F \to 0$, and the reflection disappears. The mirror BRDF is obtained in the limit $\small \eta_t/\eta_l \to \infty$ which makes $\small F \to 1$. 

The definition of the transmission component of the smooth specular BSDF is marginally more complicated by the fact that the the directions of incidence and exitance point into the opposite hemispheres:

$$ \tag{9c}
	f_t(\bm{v}, \bm{n}, \bm{l}) =
	\frac{\eta_v^2}{\eta_l^2} \big( 1 - F(\theta_l, \eta_t(\bm{l})/\eta_l) \big) \delta\big( \frac{\eta_v}{\eta_l} (\bm{v} \times \bm{n}) + (\bm{l} \times \bm{n}) \big).
$$

The leftmost term is responsible for the compression of projected solid angles, which can be readily verified by placing the surface inside a *white furnace* (by setting $\small L(\bm{l}) = 1$ for all $\small \bm{l}$) and evaluating Eqn. 4b.

$$ \tag{4b}
	L(\bm{v}) =
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{n}, \bm{l}) L(\bm{l}) d\Omega_n(\bm{l})
$$

$$ \tag{4b}
	L(\bm{v}) =
		\int_{\bm{l} \in \mathbb{S^2}}
	\frac{\eta_v^2}{\eta_l^2} \big( 1 - F(\theta_l, \eta_t(\bm{l})/\eta_l) \big) \delta\big( \frac{\eta_v}{\eta_l} (\bm{v} \times \bm{n}) + (\bm{l} \times \bm{n}) \big) d\Omega_n(\bm{l})
$$

Is this BTDF reciprocal? Naive substitution of Eqn. 9c into 4c leads to

$$ \tag{9d}
	\frac{f_t(\bm{v}, \bm{n}, \bm{l})}{f_t(\bm{l}, \bm{n}, \bm{v})}
	= \frac{\eta_v^4}{\eta_l^4}
	\frac{
		\delta\big( \eta_v (\bm{v} \times \bm{n}) + \eta_l (\bm{l} \times \bm{n}) \big)
	}{
		\delta\big( \eta_l (\bm{l} \times \bm{n}) + \eta_v (\bm{v} \times \bm{n}) \big)
	}.
$$

Unfortunately, Eqn. 9d fails to make it apparent that these two Dirac delta "functions" are not the same: their purpose is to measure two different projected solid angles (each associated with the vector in the last slot), and their ratio is precisely the same as the missing factor of $\small \eta_l^2 / \eta_v^2$. To see why that is the case, we must recall that Eqn. 7c and 9c are only valid in the context of Eqn. 6, which is an integral over the domain of the directions of incidence. If we use the reciprocal of a BSDF, the directions of incidence and exitance are interchanged, and Eqn. 6 must also be modified accordingly.

A better way to verify reciprocity is by combining Eqn. 4c, 5b, and 6:

$$ \tag{10a}
	\int_{\bm{v'} \in \mathbb{S^2}} f_s(\bm{v'}, \bm{n}, \bm{l}) d\Omega_n(\bm{v'})
	= \int_{\bm{l'} \in \mathbb{S^2}} f_s(\bm{l'}, \bm{n}, \bm{v}) d\Omega_n(\bm{l'}).
$$

If $\small \bm{v}$, $\small \bm{n}$, and $\small \bm{l}$ do not define a valid light path, both integrals vanish. Otherwise, substitution of Eqn. 9c results in identical values. In particular,

$$ \tag{10b}
\begin{aligned}
	\int_{\bm{l} \in \mathbb{S^2}} f_t(\bm{l}, \bm{n}, \bm{v}) d\Omega_n(\bm{l})
	=
	\int_{\bm{l} \in \mathbb{S^2}} \frac{\eta_{l}^2}{\eta_v^2} f_t(\bm{v}, \bm{n}, \bm{l}) d\Omega_n(\bm{l}) =
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) \le 1.
\end{aligned}
$$

Since Eqn. 7d and 10b sum up to 1, we have just demonstrated that the full BSDF is energy-conserving.

If you find reciprocity daunting, you are not alone. For instance, Eqn. 18 and 21 in Bruce Walter's widely cited paper about microfacet models for refraction are missing a factor of $\small \eta_v^2 / \eta_l^2$.

### Rough Surface BSDF

We can model a rough microsurface by treating it as locally planar. The amount of radiance scattered by the microsurface can then be expressed as a weighted average of the contributions of its visible microfacets (see Fig. 6 above).

To better understand this approach, recall that, by definition, the [radiance](/post/particle-volume/#radiometry-crash-course) is the amount of power transmitted in a certain direction, per unit solid angle associated with this direction, per unit area perpendicular to this direction. If the source of (scattered) light is very small (or very far away), it will appear point-like, and the variation of the view vector across its surface can be safely neglected. Thus,

$$ \tag{11a}
	L(\bm{v}) =
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) L(\bm{v}, \bm{p})
	V(\bm{v}, \bm{p}) dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})},
$$

which is normalized with respect to the visible projected area to convert the intensity into the radiance.

The general expression of the outgoing radiance term in the numerator of Eqn. 11a is given by the spatially varying version of Eqn. 4b:

$$ \tag{11c}
	L(\bm{v}, \bm{p}) =
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{m}(\bm{p}), \bm{l}) L(\bm{l}, \bm{p}) d\Omega_m(\bm{l}).
$$

Unfortunately, Eqn. 11c is recursive. In our case, this means that light scattered by one microfacet can illuminate another in a process known as *multiple scattering*. For simplicity, and at the cost of correctness, this effect is often neglected, with only the distant illumination (and, thus, only *single scattering*) taken into account:

$$ \tag{11d}
	L(\bm{v}, \bm{p}) \approx
	\int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{m}(\bm{p}), \bm{l}) L(\bm{l}) V(\bm{l}, \bm{p}) d\Omega_m(\bm{l}).
$$

These approximations allow us to rewrite Eqn. 11a as

$$ \tag{11e}
	L(\bm{v}) \approx
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) \int_{\bm{l} \in \mathbb{S^2}}
	f_s(\bm{v}, \bm{m}(\bm{p}), \bm{l}) L(\bm{l}) V(\bm{l}, \bm{p}) d\Omega_m(\bm{l}) dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})}.
$$

### Rough Specular BSDF

Substitution of the definition of the smooth specular BSDF given by Eqn. 7c and 9c allows us to evaluate the inner integral of Eqn. 11e analytically. Starting with the reflection component,

$$ \tag{12}
	L_r(\bm{v})
	=
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p})
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	V(\bm{r}, \bm{p}) V(\bm{v}, \bm{p})
	dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})},
$$

where

$$ \tag{13a}
	\bm{r} = \bm{R}(\bm{v}, \bm{m}) = -\bm{v} + 2 (\bm{v} \cdot \bm{m}) \bm{m}
$$

is the reflected view vector that points along the direction of incidence (see Fig. 7 below).

{{< figure src="/img/micro/scattering.svg" caption="*Figure 7. Reflected (red), refracted (or transmitted, green), and view (blue) vectors.*" >}}

According to the law of reflection, $\small \vert \bm{r} \cdot \bm{m} \vert = \vert \bm{v} \cdot \bm{m} \vert = \Vert \bm{r} + \bm{v} \Vert / 2$. If we assume that the microfacet is front-facing (and, thus, not self-occluding), then $\small (\bm{v} \cdot \bm{m}) > 0$, which allows us to solve Eqn. 13a for $\small \bm{m}$:

$$ \tag{13b}
	\bm{m} = \frac{\bm{r} + \bm{v}}{\Vert \bm{r} + \bm{v} \Vert}.
$$

In the absence of multiple scattering, we only consider distant sources of illumination; those located in the lower hemisphere are guaranteed to be occluded by the microsurface[^12], which reduces the set of potentially useful directions of incidence to those with $\small (\bm{r} \cdot \bm{n}) > 0$.

[^12]: We do not care about the literal edge cases, since a microsurface is assumed to be a fragment of a larger surface.

The transmission component of a microfacet BSDF can be defined analogously:

$$ \tag{14}
	L_t(\bm{v})
	=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	V(\bm{t}, \bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})}
	{\bm{v} \cdot \int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})},
$$

where

$$ \tag{15a}
	\bm{t} = \bm{T}(\bm{v}, \bm{m}, \eta_v/\eta_t) =
	 -\frac{\eta_v}{\eta_t}\bm{v} + \left( \frac{\eta_v}{\eta_t}(\bm{v} \cdot \bm{m}) - \mathrm{sgn}(\bm{v} \cdot \bm{m}) \sqrt{1 - \frac{\eta_v^2}{\eta_t^2} \Vert \bm{v} \times \bm{m} \Vert^2 } \right) \bm{m}
$$

is the refracted (or transmitted) view vector that points along the direction of incidence (see Fig. 7 above). The derivation of Eqn. 15a is provided in [Appendix A](#appendix-a-derivation-of-refracted-view-vector). We must caution that, in certain cases, the value of the expression inside the square root is a negative number. This invalidates the refracted direction and implies that the light has been *totally internally reflected* by the surface.

Eqn. 15a can be solved for $\small \bm{m}$ if we assume that $\small (\bm{v} \cdot \bm{m}) > 0$. The derivation is provided in [Appendix A](#appendix-a-derivation-of-refracted-view-vector); here, we simply quote the result:

$$ \tag{15b}
	\bm{m}
	= \mathrm{sgn}\negmedspace\left(\frac{\eta_v}{\eta_t} + \bm{v} \cdot \bm{t}\right) \frac{(\eta_v / \eta_t) \bm{v} + \bm{t}}{\Vert (\eta_v / \eta_t) \bm{v} + \bm{t} \Vert}.
$$

The sign function ensures that $\small (\bm{v} \cdot \bm{m}) > 0$. As for the set of potentially useful directions of incidence, distant light sources are always occluded by the microsurface unless $\small (\bm{t} \cdot \bm{n}) < 0$.

### Shadowing-Masking Function

Upon close examination of Eqn. 12 and 14, the visibility terms are the only ones that explicitly depend on the position $\small \bm{p}$. Intuitively, that is because visibility is a non-local property -- it connects a point to the entire surface.

We can evaluate both of these integrals numerically by breaking the microsurface down into individual microfacets and sorting them by their orientation. For a fixed view vector $\small \bm{v}$, a group of microfacets with the same normal $\bm{m}$ will also share the values of $\small F$ and $\small L$. The remaining factor is a surface integral that represents the total visible area of the microfacets within each group.

The statistical method of evaluation of Eqn. 12 and 14 mirrors Eqn. 2a. We must introduce the dimensionless *shadowing-masking function* $\small G_2(\bm{v}, \bm{m}, \bm{l})$ that gives the fraction of the differential area $\small dA(\bm{m})$ of the portion of the microsurface perpendicular to $\small \bm{m}$ that happens to be visible along both $\small \bm{v} \text{ and } \bm{l}$. In other words, it is the *average visibility* (along both $\small \bm{l} \text{ and } \bm{v}$) of the microfacets with the normal $\small \bm{m}$:

$$ \tag{16a}
\begin{aligned}
	&\int_{\bm{p} \in \mathbb{M^2}}
	\bm{m}(\bm{p}) V(\bm{l}, \bm{p}) V(\bm{v}, \bm{p}) dA(\bm{p})
	\cr =
	&\int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_2(\bm{v}, \bm{m}, \bm{l}) dA(\bm{m})
	\cr =
	&\int_{\bm{m} \in \mathbb{S^2}}
	\bm{m} G_2(\bm{v}, \bm{m}, \bm{l}) D(\bm{m}) A d\Omega(\bm{m}).
\end{aligned}
$$

Once projected onto $\small \bm{v} \text{ or } \bm{l}$, Eqn. 16a also represents the visible projected area, except that now the visibility is bidirectional. Similarly, it takes self-occlusion into account: $\small V = G_2 = 0$ if $\small (\bm{v} \cdot \bm{m}) \le 0 \text{ or } \small (\bm{l} \cdot \bm{m}) \le 0$.

A valid shadowing-masking function also has the following properties:

$$ \tag{16b}
\begin{aligned}
	\textit{directional symmetry: }
	&G_2(\bm{v}, \bm{m}, \bm{l}) = G_2(\bm{l}, \bm{m}, \bm{v});
	\cr
	\textit{directional correlation: }
	&G_2(\bm{v}, \bm{m}, \bm{v}) = G_1(\bm{v}, \bm{m});
	\cr
	\textit{height correlation: }
	&G_2(\bm{v}, \bm{m}, \bm{n}) = G_1(\bm{v}, \bm{m}).
\end{aligned}
$$

The last expression is only applicable to height fields[^13]. Essentially, it says that the visibility of a microfacet depends on its altitude. The second property is more general; it implies that a bidirectional average cannot be factored into the product of unidirectional averages:

[^13]: Height correlation is not limited to height fields. For arbitrary surfaces, the correlation is weaker, and it is not clear whether it is possible to express this property in a compact form.

$$ \tag{16c}
	G_2(\bm{v}, \bm{m}, \bm{l}) \ne G_1(\bm{v}, \bm{m}) G_1(\bm{l}, \bm{m}).
$$

As an example, consider a number of evenly spaced boxes -- a square wave. For $\small \bm{m} = \bm{n}$, the unidirectional visibility will approach 50% as the view angle increases. Thus, for shallow angles, $\small G_1(\bm{v}, \bm{n}) G_1(\bm{l}, \bm{n}) \approx 1/4$, while the correct value of $\small G_2(\bm{v}, \bm{n}, \bm{l}) \approx 1/2$.

<!--

### Smith's Approximation of the Shadowing-Masking Function (to be omitted)

In order to derive the shadowing-masking function from the distribution of normals, we may once again utilize Smith's assumption of normal-visibility independence:

$$ \tag{17a}
	G_2^S(\bm{v}, \bm{m}, \bm{l}) = \Theta(\bm{v} \cdot \bm{m}) \Theta(\bm{l} \cdot \bm{m}) W_2(\bm{v}, \bm{l}).
$$

**Q: geometrically, what is $\small \bm{W_2}$ supposed to be?**

The height-correlated bidirectional visibility term can be constructed by adding shadowing to the denominator of its unidirectional counterpart (see Eqn. 3Xc, 3Xd):

$$ \tag{17b}
\begin{aligned}
	W_2(\bm{v}, \bm{l})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v}) + \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{v} \cdot \bm{n}) A
	}{
		(\bm{v} \cdot \bm{n}) A + (\bm{v} \cdot \bm{n}) A \Lambda(\bm{v}) + (\bm{v} \cdot \bm{n}) A \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{v} \cdot \bm{n}) A
	}{
		\int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m}) + (\bm{v} \cdot \bm{n}) A \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{l} \cdot \bm{n}) (\bm{v} \cdot \bm{n}) A
	}{
		(\bm{l} \cdot \bm{n}) \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m}) + (\bm{v} \cdot \bm{n}) (\bm{l} \cdot \bm{n}) A \Lambda(\bm{l})
	}
	\cr
	&= \frac{
		(\bm{l} \cdot \bm{n}) A (\bm{v} \cdot \bm{n}) A
	}{
		(\bm{l} \cdot \bm{n}) A \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{v} \cdot \bm{m}) dA(\bm{m}) + (\bm{v} \cdot \bm{n}) A \int_{\bm{m} \in \mathbb{S^2}} \mathrm{max}(0, \bm{l} \cdot \bm{m}) dA(\bm{m})
	}
	\cr
\end{aligned}
$$

$$
	W_2(\bm{v}, \bm{n})
	= \frac{
		1
	}{
		1 + \Lambda(\bm{v})
	}
$$

The combined occlusion value is always either greater than or equal to the occlusion in any given direction. In particular, when the view and light vectors are aligned, overestimation leads to the loss of the so-called “hotspot effect”:

$$
	W_2(\bm{v}, \bm{v})
	= \frac{
		1
	}{
		1 + 2 \Lambda(\bm{v})
	}
	\le
	\frac{
		1
	}{
		1 + \Lambda(\bm{v}).
	}
$$

**Q: how to derive the height-correlated function geometrically, for an arbitrary surface?**

Directional correlation can be introduced by subtracting a symmetric correction term:

$$ \tag{17c}
\begin{aligned}
	W_2(\bm{v}, \bm{l})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v}) + \Lambda(\bm{l}) - g(\bm{v} \cdot \bm{l}) \sqrt{\Lambda(\bm{v}) \Lambda(\bm{l})}
	}
	\cr
\end{aligned}
$$

$$
\begin{aligned}
	W_2(\bm{v}, \bm{v})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v})
	}
	\cr
\end{aligned}
$$

$$
\begin{aligned}
	W_2(\bm{v}, \bm{n})
	&= \frac{
		1
	}{
		1 + \Lambda(\bm{v})
	}
	\cr
\end{aligned}
$$

A valid modification must satisfy $\small g(1) = 1$.

**Q: what is a good choice of g? Does it depend on roughness? Is the result still height-correlated?**

-->

### Microfacet Specular BSDF

In order to arrive at the expression of a microfacet BSDF, we must convert Eqn. 12 and 14 into a statistical form. The numerator can be rewritten in terms of the shadowing-masking function defined by Eqn. 16a. As for the denominator, according to the microfacet theory, the visible projected area of the microsurface is identical to the signed projected area of the macrosurface (see Eqn. 1-3). Therefore,

$$ \tag{18a}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) d\Omega(\bm{m})}
	{\bm{v} \cdot \bm{n}},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\bm{v} \cdot \int_{\bm{m} \in \mathbb{S^2}}
	\bm{m}
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) d\Omega(\bm{m})}
	{\bm{v} \cdot \bm{n}}.
\end{aligned}
$$

The second equation required a minor modification: we had to reverse the refracted view vector $\small \bm{t}$ because the microsurface is one-sided; refer to the section about [Inherent Assumptions](#inherent-assumptions) for details. This formulation is non-reciprocal: an equivalent (proper but verbose) expression is $\small G_2(\mathrm{sgn}(\bm{n} \cdot \bm{v}) \bm{v}, \bm{m}, \mathrm{sgn}(\bm{n} \cdot \bm{t}) \bm{t})$.

Comparison of Eqn. 18a with 4b, which serves as a definition of a BSDF, reveals that the domain of integration is not the same. Therefore, we must perform a change of variables from the microsurface normal to the direction of incidence. This can be accomplished using the *Jacobian* determinant of the transformation:

$$ \tag{18b}
	J(\bm{m}, \bm{r}) = \frac{\partial \Omega(\bm{m})}{\partial \Omega(\bm{r})},
	\quad
	J(\bm{m}, \bm{t}) = \frac{\partial \Omega(\bm{m})}{\partial \Omega(\bm{t})}.
$$

Since a determinant can be negative, we shall utilize its absolute value:

$$ \tag{18c}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\bm{v} \cdot \int_{\bm{r} \in \mathbb{S^2}}
	\bm{m}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) \big\vert J(\bm{m}, \bm{r}) \big\vert d\Omega(\bm{r})}
	{\bm{v} \cdot \bm{n}},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\bm{v} \cdot \int_{\bm{t} \in \mathbb{S^2}}
	\bm{m}
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) \big\vert J(\bm{m}, \bm{t}) \big\vert d\Omega(\bm{t})}
	{\bm{v} \cdot \bm{n}}.
\end{aligned}
$$

Technically, the Jacobian of Eqn. 18b describes the *rate* at which one solid angle changes relative to the other. The notation has been purposefully chosen to mimic the one-dimensional partial[^10] derivative $\small \partial x / \partial t$. Similarly,

[^10]: The derivative is partial because the microsurface normal implicitly depends on the view vector.

$$ \tag{18d}
	d\Omega(\bm{m})
	= \left\vert \frac{\partial \Omega(\bm{m})}{\partial \Omega(\bm{r})} \right\vert d\Omega(\bm{r})
	= \big\vert J(\bm{m}, \bm{r}) \big\vert d\Omega(\bm{r})
$$

is completely analogous to the one-dimensional change of variables $\small dx = (\partial x / \partial t) dt$.

Intuitively, the absolute value of the Jacobian is just a *ratio* of two differential solid angles, each corresponding to a projected differential area. Unfortunately, making sense of this statement (and making it more precise) requires a background in differential geometry. For those readers who are interested in the details, we provide a brief introduction to Jacobians and differential solid angles in [Appendix B](#appendix-b-jacobians-and-differential-solid-angles).

We can determine the expression of the Jacobian in Eqn. 18d using the geometric properties of differential solid angles. Taking $\small \bm{v}$ as a constant, substitution of Eqn. 13b and utilization of Eqn. B18-B21 yields

$$ \tag{19a}
	d\Omega(\bm{m})
	= d\Omega(\bm{r} + \bm{v})
	= \frac{\big\vert \bm{m} \cdot d\bm{A}(\bm{r}) \big\vert}{\Vert \bm{r} + \bm{v} \Vert^2}
	= \frac{\vert \bm{m} \cdot \bm{r} \vert}{\Vert \bm{r} + \bm{v} \Vert^2} d\Omega(\bm{r}).
$$

Thus, according to the definitions of the absolute value of the Jacobian and the law of reflection (Eqn. 7a),

$$ \tag{19b}
	\big\vert J(\bm{m}, \bm{r}) \big\vert
	= \frac{\vert \bm{r} \cdot \bm{m} \vert}{\Vert \bm{r} + \bm{v} \Vert^2}
	= \frac{1}{4 |\bm{r} \cdot \bm{m}|}.
$$

Similarly, we can use Eqn. 15b to show that

$$ \tag{20a}
	d\Omega(\bm{m})
	= d\Omega\big( (\eta_v / \eta_t) \bm{v} + \bm{t} \big)
	= \frac{\big\vert \bm{m} \cdot d\bm{A}(\bm{t}) \big\vert}{\Vert (\eta_v / \eta_t) \bm{v} + \bm{t} \Vert^2}
	= \frac{\big\vert \bm{m} \cdot \bm{t} \big\vert}{\Vert (\eta_v / \eta_t) \bm{v} + \bm{t} \Vert^2} d\Omega(\bm{t}).
$$

Therefore, according to the definitions of the absolute value of the Jacobian and the law of refraction (Eqn. 5a and A1-A4),

$$ \tag{20b}
\begin{aligned}
	\big\vert J(\bm{m}, \bm{t}) \big\vert
	&= \frac{\big\vert \bm{t} \cdot \bm{m} \big\vert}{\Vert (\eta_v / \eta_t) \bm{v} + \bm{t} \Vert^2}
	\cr
	&= \frac{\big\vert \bm{t} \cdot \bm{m} \big\vert}{\big( (\eta_v / \eta_t) \cos{\theta_v} - \cos{\theta_t} \big)^2}
	\cr
	&= \frac{\big\vert \bm{t} \cdot \bm{m} \big\vert}{\big( \sqrt{(\eta_v / \eta_t)^2 - \Vert \bm{t} \times \bm{m} \Vert^2} + (\bm{t} \cdot \bm{m}) \big)^2}.
\end{aligned}
$$

Having finally obtained the last piece of the puzzle, we can proceed with the assembly of a microfacet BSDF. Begin by substituting Eqn. 19b and 20b into 18c:

$$ \tag{21a}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\int_{\bm{r} \in \mathbb{S^2}}
	(\bm{v} \cdot \bm{m})
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m})  d\Omega(\bm{r})}
	{4 |\bm{r} \cdot \bm{m}| (\bm{v} \cdot \bm{n})},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\int_{\bm{t} \in \mathbb{S^2}}
	(\bm{v} \cdot \bm{m})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) |\bm{t} \cdot \bm{m}| d\Omega(\bm{t})}
	{\big( (\eta_v / \eta_t) \bm{v} + \bm{t} \big)^2 (\bm{v} \cdot \bm{n})}.
\end{aligned}
$$

Recall that the microfacet theory requires $\small (\bm{v} \cdot \bm{n}) > 0$. In addition, the single scattering approximation permits us to neglect the directions of incidence that fail to satisfy either $\small (\bm{r} \cdot \bm{n}) > 0 \text{ or } (\bm{t} \cdot \bm{n}) < 0$. Finally, due to the law of reflection and self-occlusion, $\small (\bm{v} \cdot \bm{m}) = (\bm{r} \cdot \bm{m}) > 0 \text{ and } (\bm{t} \cdot \bm{m}) < 0$. We may use these properties to simplify and rearrange Eqn. 21a:

$$ \tag{21b}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\int_{\bm{r} \in \mathbb{H_{+}^2}}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) |\bm{r} \cdot \bm{n}| d\Omega(\bm{r})}
	{4 (\bm{v} \cdot \bm{n}) (\bm{r} \cdot \bm{n})},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\int_{\bm{t} \in \mathbb{H_{-}^2}}
	(\bm{v} \cdot \bm{m}) (\bm{t} \cdot \bm{m})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) |\bm{t} \cdot \bm{n}| d\Omega(\bm{t})}
	{\big( (\eta_v / \eta_t) \bm{v} + \bm{t} \big)^2 (\bm{v} \cdot \bm{n}) (\bm{t} \cdot \bm{n})}.
\end{aligned}
$$

To facilitate comparison with Eqn. 4b, we shall utilize the projected solid angle notation:

$$ \tag{21c}
\begin{aligned}
	L_r(\bm{v})
	&=
	\frac{\int_{\bm{r} \in \mathbb{H_{+}^2}}
	F(\theta_v, \eta_v/\eta_t) L(\bm{r})
	G_2(\bm{v}, \bm{m}, \bm{r})
	D(\bm{m}) d\Omega_n(\bm{r})}
	{4 (\bm{v} \cdot \bm{n}) (\bm{r} \cdot \bm{n})},
	\cr
	L_t(\bm{v})
	&=
	\frac{\eta_t^2}{\eta_v^2}
	\frac{\int_{\bm{t} \in \mathbb{H_{-}^2}}
	(\bm{v} \cdot \bm{m}) (\bm{t} \cdot \bm{m})
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big) L(\bm{t})
	G_2(\bm{v}, \bm{m}, -\bm{t})
	D(\bm{m}) d\Omega_n(\bm{t})}
	{\big( (\eta_v / \eta_t) \bm{v} + \bm{t} \big)^2 (\bm{v} \cdot \bm{n}) (\bm{t} \cdot \bm{n})},
\end{aligned}
$$

which makes extracting the expressions of the BRDF and the BTDF particularly easy:

$$ \tag{21d}
\begin{aligned}
	f_r(\bm{v}, \bm{n}, \bm{l})
	&=
	\frac{
	F(\theta_v, \eta_v/\eta_t))
	G_2(\bm{v}, \bm{m}, \bm{l})
	D(\bm{m})}
	{4 (\bm{v} \cdot \bm{n}) (\bm{l} \cdot \bm{n})},
	\cr
	f_t(\bm{v}, \bm{n}, \bm{l})
	&=
	\frac{\eta_l^2}{\eta_v^2}
	\frac{(\bm{v} \cdot \bm{m}) (\bm{l} \cdot \bm{m})}
	{\big( (\eta_v / \eta_l) \bm{v} + \bm{l} \big)^2}
	\frac{
	\big( 1 - F(\theta_v, \eta_v/\eta_t) \big)
	G_2(\bm{v}, \bm{m}, -\bm{l})
	D(\bm{m})}
	{ (\bm{v} \cdot \bm{n}) (\bm{l} \cdot \bm{n})}.
\end{aligned}
$$

This concludes our derivation of a microfacet specular BSDF.

### Appendix A: Derivation of Refracted View Vector

Eqn. 15a can be derived as follows. Let us define two unit vectors

$$ \tag{A1}
\begin{aligned}
	\bm{x}
	&= \frac{\bm{m} \times (\bm{v} \times \bm{m})}{\Vert \bm{m} \times (\bm{v} \times \bm{m}) \Vert}
	= \frac{\bm{v} - (\bm{v} \cdot \bm{m}) \bm{m}}{\Vert \bm{v} \times \bm{m} \Vert},
	\cr
	\bm{y}
	&= \mathrm{sgn}(\bm{v} \cdot \bm{m}) \bm{m},
\end{aligned}
$$

such that

$$ \tag{A2}
\begin{aligned}
	\sin{\theta_v}
	&= \bm{v} \cdot \bm{x}
	= \frac{1 - (\bm{v} \cdot \bm{m})^2}{\Vert \bm{v} \times \bm{m} \Vert}
	= \Vert \bm{v} \times \bm{m} \Vert \ge 0,
	\cr
	\cos{\theta_v}
	&= \bm{v} \cdot \bm{y}
	= \vert \bm{v} \cdot \bm{m} \vert \ge 0.
\end{aligned}
$$

In this coordinate system (see Fig. 7 above),

$$ \tag{A3}
\begin{aligned}
	\bm{v}
	&= \sin{\theta_v} \bm{x} + \cos{\theta_v} \bm{y},
	\cr
	\bm{t}
	&= -(\sin{\theta_t} \bm{x} + \cos{\theta_t} \bm{y}),
\end{aligned}
$$

where

$$ \tag{A4}
\begin{aligned}
	\sin{\theta_t}
	&= \Vert \bm{t} \times \bm{m} \Vert \ge 0,
	\cr
	\cos{\theta_t}
	&= \vert \bm{t} \cdot \bm{m} \vert \ge 0,
\end{aligned}
$$

by convention.

Expansion of Eqn. A3 via substitution and simplification using the law of refraction yields Eqn. 15a:

$$ \tag{A5}
	\bm{t}
	= -\frac{\eta_v}{\eta_t}\bm{v} + \left( \frac{\eta_v}{\eta_t}(\bm{v} \cdot \bm{m}) - \mathrm{sgn}(\bm{v} \cdot \bm{m}) \sqrt{1 - \frac{\eta_v^2}{\eta_t^2} \Vert \bm{v} \times \bm{m} \Vert^2 } \right) \bm{m}.
$$

This expression has the following geometric interpretation: we reverse the direction of $\small \bm{v}$, shorten it by a factor of $\small \eta_v / \eta_t$, and stretch it along $\small \bm{m}$ until the resulting vector is of length 1.

Eqn. A3 can be solved for $\small \bm{m}$ if we assume that $\small (\bm{v} \cdot \bm{m}) > 0$, so that $\small \bm{y} = \bm{m}$. We can subsequently eliminate $\small \bm{x}$ by rescaling $\small \bm{t}$ by a factor of $\small \sin{\theta_v} / \sin{\theta_t}$:

$$ \tag{A6}
	\bm{v} + \frac{\sin{\theta_v}}{\sin{\theta_t}} \bm{t}
	= \left( \cos{\theta_v} - \frac{\sin{\theta_v}}{\sin{\theta_t}} \cos{\theta_t} \right) \bm{m}.
$$

After applying the law of refraction once more and performing a series of trivial algebraic manipulations, we readily obtain Eqn. 15b:

$$ \tag{A7}
	\bm{m}
	= \frac{\eta_v \bm{v} + \eta_t \bm{t}}{\eta_v \cos{\theta_v} - \eta_t \cos{\theta_t}}
	= \mathrm{sgn}\negmedspace\left(\frac{\eta_v}{\eta_t} + \bm{v} \cdot \bm{t}\right) \frac{(\eta_v / \eta_t) \bm{v} + \bm{t}}{\Vert (\eta_v / \eta_t) \bm{v} + \bm{t} \Vert}.
$$

### Appendix B: Jacobians and Differential Solid Angles

Consider a surface parameterized by a pair of coordinates (such as the Cartesian or the spherical coordinates):

$$ \tag{B1}
	\bm{p} = \bm{p}(x,y).
$$

The change in the surface position is given by the chain rule:

$$ \tag{B2}
	d\bm{p}
	= \frac{\partial \bm{p}}{\partial x} dx + \frac{\partial \bm{p}}{\partial y} dy.
$$

Let us define a local coordinate frame as follows:

$$ \tag{B3}
	\bm{i}
	= \frac{\partial \bm{p}}{\partial x},
	\quad
	\bm{j}
	= \frac{\partial \bm{p}}{\partial y},
	\quad
	\bm{k}
	= \bm{i} \times \bm{j}.
$$

Clearly, Eqn. B2 is a sum of two linearly independent vectors:

$$ \tag{B4}
	d\bm{p} = \bm{i} dx + \bm{j} dy.
$$

They form a tiny parallelogram that can be represented by the *vector differential area*

$$ \tag{B5}
	d\bm{A}
	= (\bm{i} dx) \times (\bm{j} dy)
	= (\bm{i} \times \bm{j}) dx dy
	= \bm{k} dx dy.
$$

Its magnitude is the scalar differential area

$$ \tag{B6}
	dA
	= \Vert d\bm{A} \Vert
	= dx dy,
$$

where we have made a simplifying assumption that the basis vectors are mutually orthogonal unit vectors.

Now, let us perform a change of variables

$$ \tag{B7}
	x = x(s,t),
	\quad
	y = y(s,t).
$$

According to the chain rule,

$$ \tag{B8}
	d\bm{p} = \frac{\partial \bm{p}}{\partial s} ds + \frac{\partial \bm{p}}{\partial t} dt.
$$

The transformed basis vectors are

$$ \tag{B9}
	\bm{i'}
	= \frac{\partial \bm{p}}{\partial s},
	\quad
	\bm{j'}
	= \frac{\partial \bm{p}}{\partial t},
	\quad
	\bm{k'}
	= \bm{i'} \times \bm{j'}.
$$

Eqn. B8 can thus be written as

$$ \tag{B10}
	d\bm{p} = \bm{i'} ds + \bm{j'} dt.
$$

The expression of the vector differential area mirrors Eqn. B5:

$$ \tag{B11}
	d\bm{A}
	= (\bm{i'} ds) \times (\bm{j'} dt)
	= (\bm{i'} \times \bm{j'}) ds dt
	= \bm{k'} ds dt.
$$

If the transformed basis is not orthonormal, the expression of the scalar differential area must account for the length of the vectors and the angle between them:

$$ \tag{B12}
	dA
	= \Vert d\bm{A} \Vert
	= \Vert \bm{k'} \Vert ds dt
	= \Vert \bm{i'} \times \bm{j'} \Vert ds dt.
$$

{{< figure src="/img/micro/jacobian.svg" caption="*Figure 8. Two surface parameterizations and the associated coordinate frames.*" >}}

Now, if we want to replace Eqn. B6 with B12 in an area integral, they must be the same[^11]. The ratio of the areas of the parallelograms formed by the basis vectors (see Fig. 8 above) is given by the absolute value of the Jacobian determinant

$$ \tag{B13}
	\vert J \vert
	= \frac{dx dy}{ds dt}
	= \frac{\Vert \bm{k'} \Vert}{\Vert \bm{k} \Vert}
	= \Vert \bm{i'} \times \bm{j'} \Vert.
$$

[^11]: The same is also true for Eqn. B5 and B11. Intuitively, both the orientation and the size (but not the shape) of the surface elements must be independent of the choice of coordinates (the tessellation scheme).

The Jacobian is also frequently used in triple integrals, where its absolute value corresponds to the ratio of the volumes of two parallelepipeds.

Once Eqn. B7 that relates the sets of coordinates has been specified, the expression of the Jacobian can be determined algebraically, by calculating the determinant of the matrix of partial derivatives. However, in our case, a geometric (coordinate-independent, basis-independent) approach is both simpler and more insightful.

First, we must point out two obvious properties of the vector differential area. It remains unchanged if the surface is translated along a constant vector $\small \bm{q}$:

$$ \tag{B14}
	d\bm{A}(\bm{p} + \bm{q}) = d\bm{A}(\bm{p}).
$$

Furthermore, the area scales quadratically with the dimensions of the surface. For a constant $\small \lambda$,

$$ \tag{B15}
	d\bm{A}(\lambda \bm{p}) = \lambda^2 d\bm{A}(\bm{p}).
$$

For a unit vector $\small \bm{m}$ (which is effectively a point on the surface of a unit sphere) and a variable $\small \lambda(\bm{m})$, the total differential in the spherical coordinates is

$$ \tag{B16}
	d(\lambda \bm{m})
	= \left( \frac{\partial \lambda}{\partial \theta} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \theta} \right) d\theta
	+ \left( \frac{\partial \lambda}{\partial \phi} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \phi} \right) d\phi.
$$

$\small \bm{m}, \partial \bm{m} / \partial \theta, \partial \bm{m} / \partial \phi$ (often called $\small \bm{r}, \bm{\theta}, \bm{\phi}$) form an orthonormal basis on the surface of a unit sphere. Thus,

$$ \tag{B17}
\begin{aligned}
	\bm{m} \cdot d\bm{A}(\lambda \bm{m})
	&= \bm{m} \cdot \left( \frac{\partial \lambda}{\partial \theta} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \theta} \right)
	\times \left( \frac{\partial \lambda}{\partial \phi} \bm{m} + \lambda \frac{\partial \bm{m}}{\partial \phi} \right) d\theta d\phi
	\cr
	&= \bm{m} \cdot \lambda^2 \left( \frac{\partial \bm{m}}{\partial \theta}
	\times
	\frac{\partial \bm{m}}{\partial \phi} \right) d\theta d\phi
	= \bm{m} \cdot \lambda^2 d\bm{A}(\bm{m}).
\end{aligned}
$$

Now, recall that, by definition, the solid angle subtended by an object is the surface area of its projection onto the unit sphere. If we consider an infinitesimal surface element and its vector area $\small d\bm{A}$ located at the point $\small \bm{p}$, the associated differential solid angle is

$$ \tag{B18}
	d\Omega(\bm{p})
	= \left\vert \frac{\bm{p}}{\Vert \bm{p} \Vert} \cdot \frac{d\bm{A}(\bm{p})}{\Vert \bm{p} \Vert^2} \right \vert.
$$

In particular, for an area element located on the surface of a unit sphere,

$$ \tag{B19}
	d\bm{A}(\bm{m}) = \bm{m} d\Omega(\bm{m}).
$$

As an angular quantity, the solid angle is scale-invariant:

$$ \tag{B20}
	d\Omega(\lambda \bm{m})
	= \left\vert \frac{\lambda \bm{m}}{\Vert \lambda \bm{m} \Vert} \cdot \frac{d\bm{A}(\lambda \bm{m})}{\Vert \lambda \bm{m} \Vert^2} \right \vert
	= d\Omega(\bm{m}).
$$

However, it is not translation-invariant due to the projection onto the unit sphere:

$$ \tag{B21}
\begin{aligned}
	d\Omega(\bm{p} + \bm{q})
	&= \left\vert \frac{\bm{p} + \bm{q}}{\Vert \bm{p} + \bm{q} \Vert} \cdot \frac{d\bm{A}(\bm{p} + \bm{q})}{\Vert \bm{p} + \bm{q} \Vert^2} \right \vert
	\cr
	&= \left\vert \frac{\bm{p} + \bm{q}}{\Vert \bm{p} + \bm{q} \Vert} \cdot \frac{d\bm{A}(\bm{p})}{\Vert \bm{p} + \bm{q} \Vert^2} \right \vert.
\end{aligned}
$$

## Acknowledgements

Writing this article would be difficult, if not impossible, without the hard work of the authors of many excellent papers about the microfacet theory and its applications, a few of which are listed below. They contain practical details, helpful illustrations, and different approaches and perspectives, which makes the perusal of these papers a great way to expand your tool set and build a more complete mental model.

## References and Suggestions for Further Reading

1. Veach, E. [Robust Monte Carlo Methods for Light Transport Simulation](https://graphics.stanford.edu/papers/veach_thesis/), Chapter 5 (1998).
2. Walter, B., Marschner, S. R., Li, H., & Torrance, K. E. [Microfacet Models for Refraction Through Rough Surfaces](https://www.graphics.cornell.edu/~bjw/papers.html) (2007).
3. Heitz, E. [Understanding the Masking-Shadowing Function in Microfacet-Based BRDFs](https://inria.hal.science/hal-00942452/en) (2014).
