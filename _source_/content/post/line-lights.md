---
title: "New Implementation of LTC Line Lights"
date: 2023-10-18
tags: [
    "Area Lighting",
    "Line Lights",
    "Linearly Transformed Cosines",
    ]
---

Recently, I had a chance to take another look at the real-time implementation of [line lights](https://blogs.unity3d.com/2017/04/17/linear-light-shading-with-linearly-transformed-cosines/). As a result, I discovered three unexpected issues:

1. The integral given by Eqn. 1.20 was evaluated incorrectly. In particular, Eqn. 1.23 should be replaced by

$$ \tag{1.23}
    F_{\bm{\omega_t}}(l) = -\frac{d}{d^2+l^2} \neq \frac{l^2}{d(d^2+l^2)}.
$$

2. Lights leak when placed just below ground. This appears to be a fundamental limitation of LTC.

3. The performance is suboptimal. It becomes apparent once the problem is approached geometrically. Then one can interpret the solution in terms of the orthogonal 2D coordinates in the plane spanned by the light and the origin. As a result, some expressions can be simplified; in particular, two arc tangents can be replaced by a single arc cosine.

<!--more-->

The new implementation that addresses these issues is given below.

```c++
#define INV_PI 0.3183098861

// This function assumes that inputs are well-behaved, e.i.
// that the line does not pass through the origin and
// that the light is (at least partially) above the surface.
float I_diffuse_line(float3 C, float3 A, float hl)
{
	// Solve C.z + h * A.z = 0.
	float h = -C.z * rcp(A.z); 	// May be Inf, but never NaN

	// Clip the line segment against the z-plane if necessary.
	float h2 = (A.z >= 0) ? max( hl, h)
						  : min( hl, h); // P2 = C + h2 * A
	float h1 = (A.z >= 0) ? max(-hl, h)
						  : min(-hl, h); // P1 = C + h1 * A

	// Normalize the tangent.
	float  as = dot(A, A); 		// |A|^2
	float  ar = rsqrt(as);  	// 1/|A|
	float  a  = as * ar;  		// |A|
	float3 T  = A * ar;	   		// A/|A|

	// Orthogonal 2D coordinates:
	// P(n, t) = n * N + t * T.
	float  tc = dot(T, C);		// C = n * N + tc * T
	float3 P0 = C - tc * T;  	// P(n, 0) = n * N
	float  ns = dot(P0, P0); 	// |P0|^2

    float nr = rsqrt(ns);       // 1/|P0|
    float n  = ns * nr;         // |P0|
    float Nz = P0.z * nr;       // N.z = P0.z/|P0|

    // P(n, t) - C = P0 + t * T - P0 - tc * T
    // = (t - tc) * T = h * A = (h * a) * T.
    float t2 = tc + h2 * a;     // P2.t
    float t1 = tc + h1 * a;     // P1.t
    float s2 = ns + t2 * t2;    // |P2|^2
    float s1 = ns + t1 * t1;    // |P1|^2
    float mr = rsqrt(s1 * s2);  // 1/(|P1|*|P2|)
    float r2 = s1 * (mr * mr);  // 1/|P2|^2
    float r1 = s2 * (mr * mr);  // 1/|P1|^2

    // I = (i1 + i2 + i3) / Pi.
    // i1 =  N.z * (P2.t / |P2|^2 - P1.t / |P1|^2).
    // i2 = -T.z * (P2.n / |P2|^2 - P1.n / |P1|^2).
    // i3 =  N.z * ArcCos[Dot[P1, P2] / (|P1| * |P2|)] / |P0|.
    float i12 = (Nz * t2 - (T.z * n)) * r2
              - (Nz * t1 - (T.z * n)) * r1;
    // Guard against numerical errors.
    float dt  = min(1, (ns + t1 * t2) * mr);
    float i3  = acos(dt) * (Nz * nr); // angle * cos(θ) / r^2

    // Guard against numerical errors.
    return INV_PI * max(0, i12 + i3);
}

// Computes 1 / length(mul(transpose(inverse(invM)), normalize(ortho))).
float ComputeLineWidthFactor(float3x3 invM, float3 ortho, float orthoSq)
{
    // transpose(inverse(invM)) = 1 / determinant(invM) * cofactor(invM).
    // Take into account the sparsity of the matrix:
    // {{a,0,b},
    //  {0,c,0},
    //  {d,0,1}}
    float a = invM[0][0];
    float b = invM[0][2];
    float c = invM[1][1];
    float d = invM[2][0];

    float  det = c * (a - b * d);
    float3 X   = float3(c * (ortho.x - d * ortho.z),
                            (ortho.y * (a - b * d)),
                        c * (-b * ortho.x + a * ortho.z));  // mul(cof, ortho)

    // 1 / length(1/s * X) = abs(s) / length(X).
    return abs(det) * rsqrt(dot(X, X) * orthoSq) * orthoSq; // rsqrt(x^2) * x^2 = x
}

float I_ltc_line(float3x3 invM, float3 center, float3 axis, float halfLength)
{
    float3 ortho   = cross(center, axis);
    float  orthoSq = dot(ortho, ortho);

    // Check whether the line passes through the origin.
    bool quit = (orthoSq == 0);

    // Check whether the light is entirely below the surface.
    // We must test twice, since a linear transformation
    // may bring the light above the surface (a side-effect).
	quit = quit || (center.z + halfLength * abs(axis.z) <= 0);

    // Transform into the diffuse configuration.
    // This is a sparse matrix multiplication.
    // Pay attention to the multiplication order
    // (in case your matrices are transposed).
    float3 C = mul(invM, center);
    float3 A = mul(invM, axis);

    // Check whether the light is entirely below the surface.
    // We must test twice, since a linear transformation
    // may bring the light below the surface (as expected).
    quit = quit || (C.z + halfLength * abs(A.z) <= 0);

    float result = 0;

    if (!quit)
    {
        float w = ComputeLineWidthFactor(invM, ortho, orthoSq);

        result = I_diffuse_line(C, A, halfLength) * w;
    }

    return result;
}
```