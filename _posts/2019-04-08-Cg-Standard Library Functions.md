---
layout: post
title:  "Cg Standard Library Functions (CG)"
date:   2019-04-08
categories: [Unity, Cg]
tags: [Unity, CG]
icon: icon-splatter
---

## **1. Math Functions**

---

## [**abs(x)**](http://developer.download.nvidia.com/cg/abs.html)

>Return the absolute value of the input parameter x.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/abs.jpg" width="40%">
</details>

---

## [**acos(x)**](http://developer.download.nvidia.com/cg/acos.html)

> Input parameter x value range is [-1,1], returns the angle value range is [0, π]  

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/acos.jpg" width="40%">
</details>

---

## [**all(x)**](http://developer.download.nvidia.com/cg/all.html)

> Returns true if input parameter x is not 0; otherwise returns false. 

---

## [**any(x)**](http://developer.download.nvidia.com/cg/any.html)

> Returns true if a boolean scalar or any component of a boolean vector is true; otherwise return false. 

---

## [**asin(x)**](http://developer.download.nvidia.com/cg/asin.html)

> Input parameter x value range is [-1, 1], return angle value range is [–π/2, π/2] 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/asin.jpg" width="40%">
</details>

---

## [**atan(x)**](http://developer.download.nvidia.com/cg/atan.html)

> The inverse tangent function, the return angle value range is [–π/2, π/2] 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/atan.jpg" width="40%">
</details>

---

## [**atan2(x)**](http://developer.download.nvidia.com/cg/atan2.html)

> Calculate the arctangent of y/x. In fact, it has almost exactly the same function as the atan(x) function, and the input parameters are different (used to prevent the error case where the division denominator is 0). Atan(x) = atan2(x, float(1)). 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/atan.jpg" width="40%">
</details>

---

## [**ceil(x)**](http://developer.download.nvidia.com/cg/ceil.html)

> Round up the input parameter x. For example: ceil(float(1.3)) with a return value of 2.0 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/ceil.jpg" width="40%">
</details>

---

## [**clamp(x,a,b)**](http://developer.download.nvidia.com/cg/clamp.html)

> Returns x clamped to the range [a,b]  
1. Returns a if x is less than a; else
2. Returns b if x is greater than b; else
3. Returns x otherwise.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/clamp.jpg" width="40%">
</details>

---

## [**cos(x)**](http://developer.download.nvidia.com/cg/cos.html)

> Returns the cosine of a in radians. The return value range is [-1,1] 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/cos.jpg" width="40%">
</details>

---

## [**cosh(x)**](http://developer.download.nvidia.com/cg/cosh.html)

> The hyperbolic cosine function calculates the hyperbolic cosine of x 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/cosh.jpg" width="40%">
</details>

---

## [**cross(a,b)**](http://developer.download.nvidia.com/cg/cross.html)

> Returns the cross product of two vectors. 
* `Note` that the input parameter must be a ternary vector!

---

## [**degrees(x)**](http://developer.download.nvidia.com/cg/degree.html)

> Input parameter x is radians, return degree. 

---

## [**determinant(m)**](http://developer.download.nvidia.com/cg/determinant.html)

> return the determinant factor of the matrix. 

---

## [**dot(a,b)**](http://developer.download.nvidia.com/cg/dot.html)

> Return the dot product of a and b 

---

## [**exp(x)**](http://developer.download.nvidia.com/cg/exp.html)

> Return the result of  $e^x$  (e = 2.71828182845904523536) 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/exp.jpg" width="40%">
</details>

---

## [**exp2(x)**](http://developer.download.nvidia.com/cg/exp2.html)

> Return the result of  $2^x$

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/exp2.jpg" width="40%">
</details>

---

## [**floor(x)**](http://developer.download.nvidia.com/cg/floor.html)

> Round down the input parameters.For example, floor(float(1.3)) returns a value of 1.0 

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/floor.jpg" width="40%">
</details>

---

## [**fmod(x)**](http://developer.download.nvidia.com/cg/fmod.html)

> returns the remainder of x/y with the same sign as x

---

## [**frac(x)**](http://developer.download.nvidia.com/cg/frac.html)

> Returns the decimal of a value or vector 

---

## [**frexp (x, out e)**](http://developer.download.nvidia.com/cg/frexp.html)

> Splits scalars and vectors into normalized fraction and a power of 2 
* `x`: Vector or scalar of which to split.
* `e`: Vector or scalar where the exponent of x is output.

---

## [**inverse(x)**](http://developer.download.nvidia.com/cg/inverse.html)

> return the inverse matrix of a matrix

---

## [**isfinite(x)**](http://developer.download.nvidia.com/cg/isfinite.html)

> Returns whether or not a scalar or each vector component is a finite value.

---

## [**isinf(x)**](http://developer.download.nvidia.com/cg/isinf.html)

> Returns whether or not a scalar or each vector component is a (negative or positive) infinite value. 

---

## [**isnan(x)**](http://developer.download.nvidia.com/cg/isnan.html)

> Returns whether or not a scalar or each vector component is not-a-number (NaN) Finite and infinite values are not NaN.

---

## [**ldexp(x,n)**](http://developer.download.nvidia.com/cg/ldexp.html)

> returns x times 2 raised to the power n. $x * 2^n$

---

## [**lerp(a,b,f)**](http://developer.download.nvidia.com/cg/lerp.html)

>  Interpolate between lower limit a and upper limit b, and f represents the weight. $(1 – f )* a + b * f$
* `Note`: that if a and b are vectors, the weight f must be a scalar or a vector of equal length.

---

## [**lit(NdotL, NdotH, m)**](http://developer.download.nvidia.com/cg/lit.html)

> The lit function is a helper function useful to compute lighting coefficients for ambient, diffuse, and specular lighting contributions. The function efficiently maps to a native instruction for most GPUs.
* **Input parameters**:
    * `NdotL`: The dot product of a normalized surface normal and a normalized light vector.
    * `NdotH`: The dot product of a normalized surface normal and a normalized half-angle vector.
    * `m`: A specular exponent, typically described as a measure of shininess. The larger the exponent, the shinier the specular highlight, the smaller the exponent, the duller the specular highlight.
* **Returns a 4D vector**: 
    * `x`: The ambient coefficient that is always 1.0.
    * `y`: The diffuse coefficient that is zero if NdotL is less than zero, and NdotL otherwise.
    * `z`: The specular coefficient that is zero if either NdotL or NdotH are less than zero, and otherwise NdotH raised to the power m.
    * `w`: Always 1.0.

---

## [**log(x)**](http://developer.download.nvidia.com/cg/log.html)

>  Returns the natural logarithm a (ln(x)).

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/log.jpg" width="40%">
</details>
---

## [**log2(x)**](http://developer.download.nvidia.com/cg/log2.html)

>  Returns the base-2 logarithm a.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/log.jpg" width="40%">
</details>
---

## [**log10(x)**](http://developer.download.nvidia.com/cg/log10.html)

>  Returns the base-10 logarithm a.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/log10.jpg" width="40%">
</details>
---

## [**max(a,b)**](http://developer.download.nvidia.com/cg/max.html)

>   returns the maximum of two scalars or each respective component of two vectors.

---

## [**min(a,b)**](http://developer.download.nvidia.com/cg/min.html)

>   returns the minimum of two scalars or each respective component of two vectors.

---

## [**modf(x, out ip)**](http://developer.download.nvidia.com/cg/min.html)

>  Decompose x into two parts, an integer and a fraction. Each part has the same sign as x, the integer part is stored in ip, and the fractional part is returned by the function.

---

## [**mul(M,N)**](http://developer.download.nvidia.com/cg/mul.html)

>  Returns the product of matrix M and matrix N.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/mul.jpg" width="40%">
</details>

---

## [**mul(M,v)**](http://developer.download.nvidia.com/cg/mul.html)

>  Returns the product of Matrix M and Vertical Vector v.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/mul2.jpg" width="40%">
</details>

---

## [**mul(v,M)**](http://developer.download.nvidia.com/cg/mul.html)

>  Returns the product of Horizontal Vector v and Matrix M.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/mul3.jpg" width="40%">
</details>

---

## [**pow(x,y)**](http://developer.download.nvidia.com/cg/pow.html)

>  return the result of $x^y$

---

## [**radians(x)**](http://developer.download.nvidia.com/cg/radians.html)

>  Degree to Radian

---

## [**round(x)**](http://developer.download.nvidia.com/cg/round.html)

>  Returns a rounded value

---

## [**rsqrt(x)**](http://developer.download.nvidia.com/cg/rsqrt.html)

>  The inverse of the square root of x(x has to be greater than 0)

---

## [**saturate(x)**](http://developer.download.nvidia.com/cg/saturate.html)

>  Limit x to [0,1]

---

## [**sign(x)**](http://developer.download.nvidia.com/cg/sign.html)

>  Returns positive one, zero, or negative one for each of the components of x based on the component's sign.
1. Returns -1 component if the respective component of x is negative.
2. Returns 0 component if the respective component of x is zero.
3. Returns 1 component if the respective component of x is positive.
4. Ideally, NaN returns NaN.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/sign.png" width="40%">
</details>

---

## [**saturate(x)**](http://developer.download.nvidia.com/cg/saturate.html)

>  Limit x to [0,1]

---

## [**sin(x)**](http://developer.download.nvidia.com/cg/sin.html)

>  Returns the sine of a in radians. The return value is in the range [-1, 1].

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/sin.png" width="40%">
</details>

---

## [**sincos(x, out s, out c)**](http://developer.download.nvidia.com/cg/sincos.html)

>  Calculate the sine and cosine at the same time, where s = sin(x) and c = cos(x). (faster than doing them separately)

---

## [**sinh(x)**](http://developer.download.nvidia.com/cg/sinh.html)

>  Returns the hyperbolic sine of x

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/sinh.png" width="40%">
</details>

---

## [**smoothstep(min,max,x)**](http://developer.download.nvidia.com/cg/smoothstep.html)

>  Interpolates smoothly from 0 to 1 based on x compared to a and b.
1. Returns 0 if x < a < b or x > a > b
2. Returns 1 if x < b < a or x > b > a
3. Returns a value in the range [0,1] for the domain [a,b].

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/smoothstep.png" width="40%">
</details>

---

## [**step(a,x)**](http://developer.download.nvidia.com/cg/step.html)

> Implement a step function returning either zero or one for each component of x

``` python
if x<a:
  return 0
else:
  return 1
```

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/step.png" width="40%">
</details>

---

## [**sqrt(x)**](http://developer.download.nvidia.com/cg/sqrt.html)

> Find the square root of x, $\sqrt{x}$  (x must be greater than 0) 

---

## [**tan(x)**](http://developer.download.nvidia.com/cg/tan.html)

> Returns the tangent of x in radians.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/tan.png" width="40%">
</details>

---

## [**tanh(x)**](http://developer.download.nvidia.com/cg/tanh.html)

> Returns the hyperbolic tangent of x.

<details>
<summary><b>Click to Show Graph</b></summary>
<img src="/static/assets/img/blog/tanh.png" width="40%">
</details>

---

## [**transpose(M)**](http://developer.download.nvidia.com/cg/transpose.html)

> Returns the transpose of the matrix M.

---


## **2. Geometric Functions**

---

## [**distance(a,b)**](http://developer.download.nvidia.com/cg/distance.html)

> returns the distance between a and b 

---

## [**faceforward(N,I,Ng)**](http://developer.download.nvidia.com/cg/faceforward.html)

>  Returns a normal as-is if a vertex's eye-space position vector points in the opposite direction of a geometric normal, otherwise return the negated version of the normal. 
``` python
if dot(Ng,I)<0 
    return N 
else 
    return -N
```

---

## [**length(v)**](http://developer.download.nvidia.com/cg/length.html)

> returns the magnitude of a vector;  
``` python
sqrt(dot(v,v))
```

---

## [**normalize(v)**](http://developer.download.nvidia.com/cg/normalize.html)

>returns a normalized vector  $ V_n = \frac{\overrightarrow{V}}{\|\overrightarrow{V}\|}$

---

## [**length(v)**](http://developer.download.nvidia.com/cg/length.html)

> returns the magnitude of a vector;  
``` python
sqrt(dot(v,v))
```

---

## [**reflect(I,N)**](http://developer.download.nvidia.com/cg/reflect.html)

>The reflection vector is calculated according to the incident ray direction I and the surface normal vector N
``` python
function reflect(I, N)
{
  return I - 2.f * Dot(I, N) * N;
}
```

---

## [**refract(I,N,eta)**](http://developer.download.nvidia.com/cg/refract.html)

> The refraction vector is calculated from the incident ray direction I, the surface normal vector N and the refraction relative coefficient eta. If the angle between I and N is too large for a given eta, return (0,0,0). (Only valid for ternary vectors) 
``` python
function refract(I, N, eta)
{
  float N_dot_I = dot(N, I);
  float k = 1.f - eta * eta * (1.f - N_dot_I * N_dot_I);
  if (k < 0.f)
    out = float3(0.f, 0.f, 0.f);
  else
    out = eta * I - (eta * N_dot_I + sqrt(k)) * N;
}
```

---

## **4. Texture Mapping Functions**

---

## [**tex1D**](http://developer.download.nvidia.com/cg/tex1D.html)
## [**tex2D**](http://developer.download.nvidia.com/cg/tex2D.html)
## [**tex3D**](http://developer.download.nvidia.com/cg/tex3D.html)
## [**texCUBE**](http://developer.download.nvidia.com/cg/texCUBE.html)

> Performs a texture lookup in sampler samp using coordinates s, may use and derivatives dx and dy, also may perform shadow comparison and use texel offset texelOff to compute final texel.

---

## [**tex1Dlod**](http://developer.download.nvidia.com/cg/tex1Dlod.html)
## [**tex2Dlod**](http://developer.download.nvidia.com/cg/tex2Dlod.html)
## [**tex3Dlod**](http://developer.download.nvidia.com/cg/tex3Dlod.html)
## [**texCUBElod**](http://developer.download.nvidia.com/cg/texCUBElod.html)

> Performs a texture lookup with specified level of detail and optional texel offset.

---

## [**tex1Dproj**](http://developer.download.nvidia.com/cg/tex1Dproj.html)
## [**tex2Dproj**](http://developer.download.nvidia.com/cg/tex2Dproj.html)
## [**tex3Dproj**](http://developer.download.nvidia.com/cg/tex3Dproj.html)
## [**texCUBEproj**](http://developer.download.nvidia.com/cg/texCUBEproj.html)

> Performs a texture lookup with projection in a given sampler. May perform a shadow comparison if argument for shadow comparison is provided.

---

## [**tex1DARRAY**](http://developer.download.nvidia.com/cg/tex1DARRAY.html)
## [**tex2DARRAY**](http://developer.download.nvidia.com/cg/tex2DARRAY.html)
## [**texCUBEARRAY**](http://developer.download.nvidia.com/cg/texCUBEARRAY.html)

> Performs a texture lookup in a given sampler array may use pre computed derivatives and, in some cases, perform a shadow comparison.

---

## [**tex1DARRAYlod**](http://developer.download.nvidia.com/cg/tex1DARRAYlod.html)
## [**tex2DARRAYlod**](http://developer.download.nvidia.com/cg/tex2DARRAYlod.html)
## [**texCUBEARRAYlod**](http://developer.download.nvidia.com/cg/texCUBEARRAYlod.html)

> Performs a texture array lookup with specified level of detail and optional texel offset.

---

## [**tex1DARRAYproj**](http://developer.download.nvidia.com/cg/tex1DARRAYproj.html)
## [**tex2DARRAYproj**](http://developer.download.nvidia.com/cg/tex2DARRAYproj.html)
## [**texCUBEARRAYproj**](http://developer.download.nvidia.com/cg/texCUBEARRAYproj.html)

> Performs a texture lookup with projection in a given sampler array. May perform a shadow comparison if argument for shadow comparison is provided.

---


## **4. Derivative Functions**

---

## [**ddx(a)**](http://developer.download.nvidia.com/cg/ddx.html)

> Returns approximate partial derivative with respect to window-space X 

---

## [**ddy(a)**](http://developer.download.nvidia.com/cg/ddy.html)

> Returns approximate partial derivative with respect to window-space Y 

---

## [**fwidth(a)**](http://developer.download.nvidia.com/cg/fwidth.html)

> Returns sum of approximate window-space partial derivatives magnitudes 

---

## **5. Debugging Function**

---

## [**void debug(float4 x)**](http://developer.download.nvidia.com/cg/ddy.html)

> If DEBUG is set at compile time, calling this function in the fragment coloring program can use the value x as the final output of the COLOR semantics; otherwise the function does nothing.


---

---

> [Reference Link](http://developer.download.nvidia.com/cg/index_stdlib.html)

>**End --Cheng Gu**

