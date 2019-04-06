---
draft: true
title: "Understanding the Surface Gradient"
date: 2019-04-06T15:39:51-07:00
categories: [ "Graphics", "Math" ]
tags: [
	"Height Mapping",
	"Normal Mapping",
	"Surface Gradient",
	]
---

Realistic rendering at high frame rates remains at the core of real-time computer graphics. High performance and high fidelity are often at odds, requiring clever tricks and approximations to reach the desired quality bar.

One of the oldest tricks in the book is [bump mapping](https://www.microsoft.com/en-us/research/publication/simulation-of-wrinkled-surfaces/). Introduced in 1978 by Jimm Blinn, it is a simple way to add mesoscopic detail without increasing the geometric complexity of the scene. Most modern real-time renderers support a variation of this technique called normal mapping. While it's fast and easy to use, certain operations, such as [normal map blending](https://blog.selfshadow.com/publications/blending-in-detail/), can be a challenge. This is where the so-called "surface gradient framework" comes into play.

The surface gradient framework is a set of tools to transform and combine normal maps originally introduced by Morten S. Mikkelsen in his 2010 paper titled ["Bump Mapping Unparametrized Surfaces on the GPU"
](http://jbit.net/~sparky/sfgrad_bump/mm_sfgrad_bump.pdf), and further extended to handle triplanar mapping in his subsequent [blog post](http://mmikkelsen3d.blogspot.com/2013/10/volume-height-maps-and-triplanar-bump.html). While there's nothing wrong with these two, to really understand what's going on, it really helps to also read his [thesis](http://image.diku.dk/projects/media/morten.mikkelsen.08.pdf), and at 109 pages, that's quite a time investment.

Instead, I will attempt to give a shorter derivation, assuming no prior knowledge of the topic, starting from the the first principles. Still with me? Let's dive in.

<!--more-->

## Definitions



[Blinn 1978]     James F. Blinn,      Simulation of Wrinkled Surfaces.
[Mikkelsen 2008] Morten S. Mikkelsen, Simulation of Wrinkled Surfaces Revisited.
[Mikkelsen 2010] Morten S. Mikkelsen, Bump Mapping Unparametrized Surfaces on the GPU.

// TODO: object scale mess

0. Definitions

{a, b} - column vector
[A, B] - matrix composed of two column vectors
A~u    - derivative of A w.r.t. u: A~u = ∂A/∂u

{u, v} - known surface parametrization
{s, t} - texture coordinates after an affine transformation: {s, t} = {a * u + b, c * v + d}
K      - object scale matrix, non-uniform

H      - scalar bump (height) map
G_h    - gradient  of bump map: G_h = {H~u, H~v} = {∂H/∂u, ∂H/∂v}
T_h    - tangent   of bump map: G_h = {1, 0, H~u} / ||{1, 0, H~u}||
T_h    - bitangent of bump map: G_h = {0, 1, H~v} / ||{0, 1, H~v}||
N_h    - normal    of bump map: N_h = {-H~u, -H~v, 1} / ||{-H~u, -H~v, 1}||

P      - geometric surface with a known parametrization (e.g. a triangle or a quad): P(u, v) = {P.x, P.y, P.z}
T_p    - tangent   of geometric surface: T_p = P~u = ∂P/∂u, not normalized, not orthogonal to B_p
B_p    - bitangent of geometric surface: B_p = P~v = ∂P/∂v, not normalized, not orthogonal to T_p
N_p    - normal    of geometric surface: N_p = (T_p x B_p) / ||T_p x B_p||
F_p    - tangent frame (matrix) of geometric surface: F_p = [T_p, B_p, N_p]

S      - smooth surface given by interpolated vertex normals
T_s    - tangent   of smooth surface
B_s    - bitangent of smooth surface
N_s    - normal    of smooth surface

D      - displaced ("bumped") surface
T_d    - tangent   of displaced surface
B_d    - bitangent of displaced surface
N_d    - normal    of displaced surface
G_d    - gradient  of displaced surface

B'u
B`u
B~u

1.1. Bump Mapping of a Geometric Surface (u, v)

Originally, Blinn proposed the following equation [Blinn 1978, section 2]:

D(u, v) = P(u, v) + H(u, v) * N_p(u, v)                                                             (1)

This equation applies a displacement in world units (e.g. in meters).

T_d = D~u = (P + H * N_p)~u = T_p + H~u * N_p + H * N_p~u                                           (2)
B_d = D~v = (P + H * N_p)~v = B_p + H~v * N_p + H * N_p~v                                           (3)

Using Blinn's approximation [Blinn 1978, section 2]:

// TODO: not a good approximation with smooth normals?
// TODO: double check what Morten does
N_p~u -> 0, N_p~v -> 0                                                                              (4)
T_d ≈ T_p + H~u * N_p                                                                               (5)
B_d ≈ B_p + H~v * N_p                                                                               (6)

N_d = (T_d x B_d) / ||T_d x B_d||                                                                   (7)
N_d ≈ (T_p x (B_p + H~v * N_p) + (H~u * N_p) x (B_p + H~v * N_p)) / ||...||                         (8)
N_d ≈ ((T_p x B_p) + H~v * (T_p x N_p) + H~u * (N_p x B_p) + H~u * H~v * (N_p x N_p)) / ||...||     (9)
N_d ≈ ((T_p x B_p) + H~v * (T_p x N_p) + H~u * (N_p x B_p)) / ||...||                               (10)

We can rescale both the numerator and the denominator by (1 / ||T_p x B_p||):

N_d ≈ (N_p + H~v * (T_p x N_p) / ||T_p x B_p|| + H~u * (N_p x B_p) / ||T_p x B_p||) / ||...||       (11)

We can define the (rescaled) gradient of the displaced surface as follows:

G_d = H~u * (B_p x N_p) / ||T_p x B_p|| + H~v * (N_p x T_p) / ||T_p x B_p||                         (12)
N_d ≈ (N_p - G_d) / ||...||                                                                         (13)

Notice that the surface gradient is simply the height map gradient after a change of basis from
[{1, 0}, {0, 1}] to [(B_p x N_p) / ||T_p x B_p||, (N_p x T_p) / ||T_p x B_p||].

Using the matrix notation [Mikkelsen 2008, section 2.2], we can define the tangent-to-world
transformation F_p and its inverse F_p⁻¹:

F_p      = [T_p, B_p, N_p]                                                                          (14)
F_p⁻¹    = adj(F_p) / det(F_p)                                                                      (15)
det(F_p) = (T_p x B_p) · N_p = ||T_p x B_p||                                                        (16)
adj(F_p) = [B_p x N_p, N_p x T_p, T_p x B_p]ᵀ                                                       (17)
F_p⁻¹    = (1 / ||T_p x B_p||) * [B_p x N_p, N_p x T_p, T_p x B_p]ᵀ                                 (18)

Using the inverse-transpose allows us to reformulate the bump mapping equation (13) as a
tangent-to-world transformation:

N_d ≈ ((F_p⁻¹)ᵀ * {-H~u, -H~v, 1}) / ||...||                                                        (19)
N_d ≈ ((F_p⁻¹)ᵀ * N_h) / ||...||                                                                    (20)

1.2. Bump Mapping of a Reparametrized Geometric Surface (s, t)

The extension to scaled and biased texture coordinates is fairly straightforward.
One issue to be aware of is that the new parametrization may not be orientation preserving,
e.i. the tangent frame may flip. We need to make sure that the resulting normal is not flipped.

D(s, t) = P(s, t) + H(s, t) * N_p(s, t)                                                             (21)

T_d = D~s = (P + H * N_p)~s ≈ P~s + H~s * N_p                                                       (22)
B_d = D~t = (P + H * N_p)~t ≈ P~t + H~t * N_p                                                       (23)


N_d = (T_d x B_d) / ((T_p x B_p) · N_p)




1.3. Accounting for Object Scale

Blinn's original formulation causes the displaced surface to flatten as the scale of the object
is increased [Blinn 1978, section 4]. Assuming that we operate in the object space for simplicity,
one solution is to rescale both the surface and the displacement along the normal:

D(u, v) = K * (P(u, v) + H(u, v) * N_p(u, v))                                                       (21)
D(u, v) = P_k(u, v) + H(u, v) * N_p_k(u, v)                                                         (22)

If operating in the world space rather than the object space, the scale vector K needs to be rotated
from the object to the world space, and then the the math below still holds.

T_d ≈ T_p_k + H~u * N_p_k                                                                            (23)
B_d ≈ B_p_k + H~v * N_p_k                                                                            (24)
N_d = (T_d x B_d) / ||T_d x B_d||                                                                   (25)
N_d ≈ (T_p_k x (B_p_k + H~v * N_p_k) + (H~u * N_p_k) x (B_p_k + H~v * N_p_k)) / ||...||                (26)
N_d ≈ ((T_p_k x B_p_k) + H~v * (T_p_k x N_p_k_k) + H~u * (N_p_k_k x B_p)) / ||...||                   (25)
N_d ≈ (N_p + H~v * (T_p x N_p_k) / ||T_p x B_p|| + H~u * (N_p_k x B_p) / ||T_p x B_p||) / ||...||     (25)

This means that the new surface gradient is a distorted version of the original one.

Using the matrix notation, ...

F_p      = [T_p, B_p, N_p_k]                                                                        (25)
F_p⁻¹    = adj(F_p) / det(F_p)                                                                      (26)
det(F_p) = (T_p x B_p) · N_p_k                                                                      (27)
adj(F_p) = [B_p x N_p_k, N_p_k x T_p, T_p x B_p]ᵀ                                                   (28)
F_p⁻¹    = (1 / ((T_p x B_p) · N_p_k)) * [B_p x N_p_k, N_p_k x T_p, T_p x B_p]ᵀ                     (29)

N_d ≈ ((F_p⁻¹)ᵀ * {-H~u, -H~v, 1}) / ||...||                                                          (30)
N_d ≈ ((T_p x B_p) / ((T_p x B_p) · N_p_k))



2. Bump Mapping of a Smooth Surface