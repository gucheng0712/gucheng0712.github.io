---
layout: post
title:  "PBR Theory (CG)"
date:   2019-05-01
categories: [Cg]
tags: [CG]
icon: icon-splatter
---

# Table of Contents
### 1. [**什么是PBR? 为什么要用PBR?**](#1)
### 2. [**光的原理**](#2)
### 3. [**辐射测量学物理量(Radiometric Physics)**](#3)
### 4. [**渲染方程(Render Equation)**](#4)
### 5. [**精确光源(Punctual Light Sources)**](#5)
### 6. [**双向反射分布函数(BRDF)**](#6)
### 6.1. [**漫反射项(Diffuse Term)**](#6.1)
### 6.2. [**高光反射项(Specular Term)**](#6.2)
### 7. [**Custom PBS Shader in Unity**](#7)

<a name="1"></a>

<br/><br/>

---

<br/><br/>

# 1. 什么是PBR? 为什么要用PBR?

<br/><br/>

**基于物理的渲染(physical based rendering, PBR)**是计算机图形学中的一种方法，它使用具有物理意义的参数和真实物理定律来渲染逼真的照片。

> 使用PBR的**原因**主要有两种:
1. 渲染出更真实的画面.
2. 更方便于美术人员调整参数以得到满意的效果,加快工作流.

<br/><br/>

<p align="center">     
<img src="/static/assets/img/blog/pbr.jpg" width="70%">
</p>

> PBR基础理论的概括:
1. 微平面理论（Microfacet Theory）: 微平面理论是将物体表面建模成做无数微观尺度上有随机朝向的理想镜面反射的小平面（microfacet）的理论。在实际的PBR 工作流中，这种物体表面的不规则性用粗糙度贴图或者高光度贴图来表示。
2. 能量守恒（Energy Conservation）: 出射光线的能量永远不能超过入射光线的能量。随着粗糙度的上升镜面反射区域的面积会增加，作为平衡，镜面反射区域的平均亮度则会下降。
3. 菲涅尔反射（Fresnel Reflectance）: 光线以不同角度入射会有不同的反射率。相同的入射角度，不同的物质也会有不同的反射率。万物皆有菲涅尔反射。F0是即 0 度角入射的菲涅尔反射值。大多数非金属的F0范围是0.02~0.04，大多数金属的F0范围是0.7~1.0。
4. 线性空间（Linear Space）: PBR的光照计算必须在线性空间完成，shader 中输入的gamma空间的贴图比如漫反射贴图需要被转成线性空间，在具体操作时需要根据不同引擎和渲染器的不同做不同的操作。而描述物体表面属性的贴图如粗糙度，高光贴图，金属贴图等必须保证是线性空间。
5. 色调映射（Tone Mapping）: 是将宽范围的照明级别拟合到屏幕有限色域内的过程。因为基于HDR渲染出来的亮度值会超过显示器能够显示最大亮度，所以需要使用色调映射，将光照结果从HDR转换为显示器能够正常显示的LDR。
6. 物质的光学特性（Substance Optical Properties）: 现实世界中有不同类型的物质可分为三大类：绝缘体（Insulators），半导体（semi-conductors）和导体（conductors）。在渲染和游戏领域，我们一般只对其中的两个感兴趣：导体（金属）和绝缘体（电解质，非金属）。其中非金属具有单色/灰色镜面反射颜色。而金属具有彩色的镜面反射颜色。即非金属的F0是一个float。而金属的F0是一个float3，如下图。

<p align="center">     
<img src="/static/assets/img/blog/substance_optical_properties.jpg" width="50%">
</p>

<a name="2"></a>


<br/><br/>

--- 

<br/><br/>

# 2. 光的原理

<br/><br/>

**光线**是由光源发射出来的,与物体相交后一部分会被吸收(absorption),另一部分会被散射(scattering).

<p align="center">
<img src="/static/assets/img/blog/flat_surface.png" width="50%">
</p>

光在与**非光学平坦表面（Non-Optically-Flat Surfaces）**的交互时，非光学平坦表面表现得像一个微小的光学平面表面的大集合。表面上的每个点都会以略微不同的方向对入射光反射，而最终的表面外观是许多具有不同表面取向的点的聚合结果。在微观尺度上，**表面越粗糙，反射越模糊**，因为表面取向与整个宏观表面取向的偏离更强。如下图:

<p align="center">
<img src="/static/assets/img/blog/v2-non-flat_surface.jpg" width="60%">
</p>

**菲涅尔等式(Fresnel Equation)**描述的是有多少百分比的光会被反射,剩余百分比的光则会被散射.

**金属(Metal)和非金属(Non-Metal):** 金属材质具有很高的吸收系数,所有被折射的光会被立即吸收,被金属内部的自由电子转化成其他形式的能量.所以几乎没有散射. 而非金属材质会出现吸收和散射两种现象, 背面散射出去的光被称为次表面散射光(Subsurface-Scattered Light). 
<p align="center">
<img src="/static/assets/img/blog/metal.jpg" width="40%">
<img src="/static/assets/img/blog/non-metal.jpg" width="40%">
</p>

**次表面散射光(Subsurface-Scattered Light):** 次表面散射其实和漫反射的物理现象本质是相同的,都是折射光的次表面散射的结果. 唯一的区别是相对与观察尺度的散射距离.光的折射现象，建模为漫反射还是次表面散射，取决于观察的尺度. 如下图: 

<p align="center">
<img src="/static/assets/img/blog/sss.jpg" width="70%">
</p>

在左上图,像素（带有红色边框的绿色圆形）大于光线离开表面之前所经过的距离。 在这种情况下，可以假设出射光从入口点（右上）射出，可以当做漫反射，用局部着色模型处理.
然而在底部的图片中, 像素小于散射距离; 如果需要更真实的着色效果，则不能忽略这些距离的存在，需当做次表面散射现象进行处理。

<a name="3"></a>

<br/><br/>

--- 

<br/><br/>

# 3. 辐射测量学物理量

<br/><br/>

**立体角(solid angle):** 立体角是以圆锥体的顶点为心,半径为1的球面被锥面所截得的面积来度量的,度量单位称为“立体弧度”.和平面角的定义类似.在平面上我们定义一段弧微分S与其矢量半径r的比值为其对应的圆心角记作 $ dθ=\frac{ds}{r} $, 所以整个圆周对应的圆心角就是2π；与此类似,定义立体角为曲面上面积微元ds与其矢量半径的二次方的比值为此面微元对应的立体角记作$dw=\frac{ds}{r^2}$. 由此可得,闭合球面的立体角都是4π. (球面度(sr)是立体角的国际单位)

<p align="center">     
<img src="/static/assets/img/blog/solidangle.png" width="20%">
</p>

<br/>

**光通量(luminous flux):** 指人眼所能感觉到的辐射功率，它等于单位时间内某一波段的辐射能量和该波段的相对视见率的乘积。(单位:$W$).用 $\phi$ 表示

```
光通量计算：

灯具实际光通量（灯具流明）/（单颗光源光通量*光源数量），说浅一点就是灯具实际发出的光通量除以灯具应该发出的光通量。

例：灯具有五颗1W大功率光源，单颗大功光源光通量为80LM，整灯应该发出80LM*5PCS=400LM。但当组装成灯具后，实际灯具光通量只有350LM了。这就是发光效率。那此灯的光效就是350LM/400LM=87.5%
```

<br/>

**辐射照度(irradiance):** 单位面积的光通(单位:$W/m^2$). $E = \frac{d\phi}{dA}$ (dA表示接收面)

<br/>

**光强(intensity):** 单位立体角光通(单位$W/sr$). $I =\frac{d\phi}{dw}$ 

<br/>

**辐射亮度(radiance):** 表示单位面积,单位方向上光源的辐射通量(单位$W/m^2/sr$). 
$$L = \frac{d\phi}{dw*dA*cos\theta}$$

<p align="center">
<img src="/static/assets/img/blog/radiance.png" width="80%">
</p>

<p align="center">
<img src="/static/assets/img/blog/radiance2.png" width="80%">
</p>

<a name="4"></a>
<br/><br/>

--- 

<br/><br/>

# 4. 渲染方程
<br/><br/>
​**渲染方程(Rendering Equation)**是计算机图形学的核心公式，当去掉其中的自发光项 *$L_e(v)$*后，剩余的部分就是著名的反射等式(**ReflectanceEquation**)。

​想象我们现在要计算表面上某点的出射辐射率，我们已知到该点的观察方向，该点的出射辐射率是由从许多不同方向的入射辐射率叠加后的结果。其中，$f(w_i, v)$表示了不同方向的入射光在该观察方向上的权重分布。我们把这些不同方向的光辐射率$L_i(w_i)$部分)乘以观察方向上所占的权重部分$f(w_i,v)$，再乘以它们在该表面的投影结果($n \cdot w_i$)部分，最后再把这些值加起来(即做积分)就是最后的出射辐射率。

自从James Kajiya发明了渲染方程, 我们不用再靠各种各样的想法以及测试来尝试进行各种渲染, 我们有了统一的目标,那就是更好的求解渲染方程. 公式如下:

$$L_o(v) = L_e(v)+\int_{\Omega}{f(w_i,v)L_i(w_i)(n\cdot w_i)dw_i}$$


> $L_o (v)$ : 由当前点出发到观察点(v)的总光照
<br/>$L_e(v)$: 由当前点发出的自发光部分
<br/>$\int_{\Omega}$: 对以当前点为球心的半球内的入射光照进行无穷小的累积,即求积分.
<br/>$f(w_i,v)$: 描述了从某个方向出发经过当前点反射后,有多少光照被反射到了观察方向.
<br/>$L_i(w_i)$: 从某个方向出发,到达当前点的光照.
<br/>($n\cdot w_i$): 由于入射角度二队从该方向出发的光照亮度所造成的衰减.
<br/>$dw_i$: 入射方向位于以当前点为球心的半球内的某个入射光照.  

<p align="center">     
<img src="/static/assets/img/blog/rendering equation.png" width="50%">
</p>

在实时渲染中，自发光项通常就是直接加上某个自发光值。除此之外，积分累加部分在实时渲染中也基本无法实现，因此积分部分通常会被若干***精确光源***的叠加所代替，而不需要计算所有入射光线在半球面上的积分。

<a name="5"></a>
<br/><br/>

--- 

<br/><br/>

# 5. 精确光源(Punctual Light Sources)
<br/><br/>
在真实的物理世界中,所有的光源都是有面积概念的,即所谓的**面光源**. 由于面光源的光照计算通常要耗费大量的时间,因此在实时渲染中, 我们通常会使用精确光源(punctual light sources). 图形学中常见的精确光源有点光源, 平行光, 和聚光灯等. 这些精确光源被认为是大小无限小而且方向是确定的. 尽管这不符合真实的物理意义,但是它们在大多是情况下都能得到较好的渲染效果.

>好处: 大大简化反射等式(Reflection Equation)

对于**一个精确光源**,可以用一下等式来计算它在某个观察方向(v)上的出射辐射率:$$L_o(v) = \pi f(l_c,v)c_{light}(n\cdot l_c)$$


> $l_c$: 表示了它的方向
<br/>
$c_{light}$:表示了它的颜色6


然而对于**多个精确光源**, 只要累积相加结果即可:$$L_o(v) = \sum_{i=0}^nL_o^i(v) = \sum_{i=0}^n\pi f(l_c^i,v)c_{light}(n\cdot l_c^i)$$

*$f(l_c^i,v)$*实际上􏰁述了当前点是如何与入射光线进行交互的:当给定某个入射方向的入射光后，有多少百分比的光照被反射到了观察方向上。在图形学中，这一项有一个专门的名字，那就是**双向反射分布函数**，即 **BRDF**。

<a name="6"></a>
<br/><br/>

--- 

<br/><br/>

# 6. 双向反射分布函数(BRDF)
<br/><br/>
**BRDF(Bidirectional Reflectance Distribution Function)**描述了物体表面一点是如何和光进行交互的. 大多数情况下,BRDF可以用$f(l,v)$ 来表示,其中 $l$ 为入社方向, $v$ 为观察方向(双向的含义). 分为**各向同性(isotropic)**的BRDF, 和**各项异性(Anisotropic)**的BRDF. 

各向同性和各向异性是指物理性质在不同的方向进行测量得到的结论。如果各个方向的测量结果是相同的，说明其物理性质与取向无关，就称为各向同性。如果物理性质和取向密切相关，不同取向的测量结果迥异，就称为各向异性。

> 例如, 绕着表面法线旋转入射方向或观察方向并不会影响BRDF的结果,那么这种BRDF被称为是各项同性,反之则是各项异性.

<br/>

BRDF 有两种理解方式:
1. 当给定入射角度后，BRDF 可以给出所有出射方向上的反射和散射光线的相对分布情况.
2. 当给定观察方向(即出射方向)后，BRDF 可以给出从所有入射方向到该出射方向的光线分布.
> 简单的说就是当一束光线沿着入射方向$l$ 到达表面某点时, $f(l,v)$ 表示了有多少能量被反射到了观察方向$v$ 上.

需要满足**两个特性**:

1. 交换律(**reciprocity**): 当交换 ***l*** 和 ***v*** 的值后，BRDF 的值不变

$$f(l,v) = f(v,l)$$

2. 能量守恒(**energy conservation**): 表面反射的能量不能超过入射的光能

$$\int_{\pi}f(l,v)(n\cdot l)dw_o\le1$$

基于这些理论, BRDF 可以用于描述两种不同的物理现象:**表面反射**和**次表面散射**。针对每种现象，BRDF通常会包含一个单独的部分来描述它们— 用于描述表面反射的部分被称为高光反射项(**specular term**)，以及用于描述次表面散射的漫反射项(**diffuse term**). (如下图)

<p align="center">     
<img src="/static/assets/img/blog/brdf.png" width="50%">
</p>

<a name="6.1"></a>
<br/><br/>

--- 

<br/><br/>

# 6.1. 漫反射项(Diffuse Term)
<br/><br/>
## **1. Lambertian BRDF:** 

$$f_{Lambert}(l,v) = \frac{c_diff}{\pi}$$

> $c_{diff}$:表示漫反射的颜色. 比起普通的漫反射,BRDF要除以$\pi$, 因为我们假设漫反射在所有方向上的强度都是相同的,而BRDF要求在半球内的积分为1. 因此,给定入射方向$l$的光源在表面某点的出射漫反射辐射率(辐射亮度)的公式: 

$$L_o(v) = \pi f_{Lambert}(l,v)c_{light}(n\cdot l) = c_{diff} \times c_{light}(n\cdot l)$$

> 使用Lambert模型作为PBS模型的的优点**,计算量小,性能比较好**. 而缺点就是在**真实世界中很少有材质符合Lambert的数学描述,即具有完美均匀的散射效果**.

## **2. Disney BRDF:** 

$$f_diff(l,v) = \frac{baseColor}{\pi}(1+(F_{D90}-1)(1-n\cdot l)^5)(1+(F_{D90}-1)(1-n\cdot v)^5)$$

> 其中, $F_{D90} = 0.5 + 2roughness(h\cdot l)^2$, baseColor是表面颜色, 通常有纹理采样得到,roughness是表面的粗糙度. 

> 上面的漫反射项既考虑了在掠射角漫反射项的能量变化，还考虑了表面的粗糙度对漫反射的影响. Disney使用了 Schlick 菲涅耳近似等式[7]来模拟在掠射角的反射变化，同时使用表面粗糙度来进一步修改它，这使得光滑材质可以在掠射角具有更为明显的阴影边，而又使得粗糙材质在掠射角具有亮边。而上面的式子也正是 Unity 5 内部使用的漫反射项.


<a name="6.2"></a>
<br/><br/>

--- 

<br/><br/>

# 6.2. 高光反射项(Specular Term)
<br/><br/>
> 在现实生活中,几乎所有的物体都或多或少有高光反射现象. Everything is shiny.
在基于物理的渲染中的BRDF的高光反射项大多是建立在**微面元理论(microfacet theroy)**的假设上的.

**微面元理论(microfacet theory):** 指物体表面实际是由许多人岩看不到的微面元组成,**虽然物体表面并不是光学平滑的,但是这些微面元可以被认为是光学平滑的**,也就是说他们具有完美的高光反射.

当光和这些微面元相交时,光线会被分割成两个方向 — 反射方向和折射方向. 高光反射只需要考虑被反射光线,折射光线是漫反射所需要考虑的部分. (微面元理论仍然有缺陷, 因为有一些材质是无法使用微面元理论来描述的)

假设物体表面法线为$n$, 这些微面元的法线$m$并不都等于n,因此,不同的微面元会把统一入射方向的光线反射到不同的方向上. 而当我们计算BRDF时,入射方向$l$和观察方向$v$都会被给定, 这意味着只有一部分微面元反射的光线才会进入到我们的眼睛中, 这部分微面元会恰好把光线反射到方向 $v$ 上,即它们的法线 $m$ 等于 $l$ 和 $v$ 的一半, 称为版角度矢量 $h$ (half vector). 如下图

<p align="center">     
<img src="/static/assets/img/blog/microfacet1.png" width="50%">
</p>

那些 $m = h$ 的微面元会恰好把入射光从 $l$ 反射到 $v$ 上，只有这部分微面元才可以添加到 BRDF 的计
算中. 然而,这些 $m=h$ 微面元反射也有一部分不会被添加到BRDF的计算中,这是因为,它们其中一部分会在入射方向 $l$ 上呗其他微面元挡住(shadowing). 或者是它们的反射方向 $v$ 上被其他微面元挡住了(masking). 如下图

<p align="center">     
<img src="/static/assets/img/blog/shadowing and masking.png" width="50%">
</p>

<br/>
## **Torrance-Sparrow 微面元模型:** 
<br/>
$$f_{spec}(l,v) = \frac{F(l,h)G(l,v,h)D(h)}{4(n\cdot l)(n\cdot v)}$$

> $D(h)$: 微面元的法线分布函数(normal distribution function, NDF), 用于计算有多少比例的微面元的法线满足 m=h, 只有这部分微面元才会把光线从 $l$方向反射到 $v$ 上.
>
> **$G(l,v,h)$:** 是阴影遮掩函数(shadowing-masking function), 它用于计算那些满足 m=h 的微面元有多少回由于遮挡而不会被人眼看到. 从而给出了活跃的微面元(active microfacets)所占的浓度,只有活跃的微面元才会成功的把光线反射到观察方向上. 
>
> **$F(l,h):$** 是这些活跃微面元的菲涅尔反射函数(Fresnel Reflectance function). 他可以告诉我们每个活跃的微面元会把多少入射光线反射到观察方向上, 即表示了反射光线占入射光线的比率.
>
> **$4(n\cdot l)(n\cdot v): $**用于校正从微面元的局部空间到整体宏观表面数量差异的校正因子.

 
> 这些不同的部分又可以衍生出很多不同的 BRDF 模型.

<br/>
## a. 菲涅尔反射函数 (Fresnel Reflectance Function)
<br/>
菲涅尔反射函数计算了光学表面反射光线所占的部分,它表明了当光照方向和观察方向夹角逐渐增大时高光反射强度增大的现象.

Schlick菲涅尔近似等式是比较常用的,公式如下:

$$F_{Schlick}(l,h) = c_{spec} + (1 - c_{spec})(1-(l\cdot h))^5$$

> **$c_{spec}:$** 高光反射的颜色. 金属材质的高光反射颜色值比较大,非金属材质的反射颜色值比较小.

<br/>
## b. 法线分布函数 (Normal Distribution Function)
<br/>
法线分布函数$D(h)$表示了对于当前表面来说有多少比例的微面元的法线满足 $m=h$, 这意味着只有这些微面元才会把光线从 $l$ 方向反射到 $v$ 上. 

<br/>
### Blinn-Phong模型使用的法线分布函数: $D_{blinn}(h) = \frac{gloss+2}{2\pi}(n\cdot h)^{gloss}$
<br/>
> $gloss$是于表面粗糙度相关的函数, 它的值可以是任意非负数.

> 然而Blinn-Phong模型并不能真实地范引出真实世界中物体的微面元法线方向分布,因此有了更复杂的分布函数像GGX,  Beckmann等

<br/>
### GGX法线分布函数: $D_{GGX}(h) = \frac{\alpha ^2}{\pi((\alpha ^2-1)(n\cdot h)^2+1)^2}$
<br/>

> $\alpha = roughness^2$ 

> 与 Blinn-Phong 的法线分布相比，GGX 分布具有更明亮、更狭窄且拖尾更长的高光区域

<p align="center">     
<img src="/static/assets/img/blog/Specular D.jpg" width="50%">
</p>

> 在 Disney BRDF 中，Disney 认为对于很多材质来说，GGX 表现出来的高光拖尾仍然不够长。
> 他们选择使用一种更加广义的法线分布模型，即 Generalized-Trowbridge-Reitz(GTR)分布。GTR
> 分布于 GGX 分布很类似，但它的分母部分的指数不是 2，而是一个可调参数。Disney 使用两个不
> 同指数的 GTR 分布作为两个高光反射片，其中第一个反射片用于表示基本材质层，第二个反射片
> 用于表示基本材质表面的清漆层。除此之外，他们还发现令 *α* *= roughness**2* 可以在材质粗糙度上得
> 到更加线性的变化。否则，直接使用 *roughness* 作为参数的话会导致在光滑材质和粗糙材质之间
> 插值出来的材质总是偏粗糙的。

<br/>
## c. 阴影-遮挡函数 (Masking and Shadowing)
<br/>

阴影遮挡函数 $G(l,v,h)$也被称为几何函数(geometry function). 它表明了具有给定面法线$m$ 的微面元在沿着入射方向 $l$和观察方向 $v$ 上不会被其他微元面挡住的概率. 公式如下:

$$G_{implicit}(l,v,h) = (n\cdot l_c)(n\cdot v)$$

> 但是$G_{implicit}$的实现忽略了材质粗糙度的影响,缺乏一定的物理真实性.

比较常见的几何项G的模型有:
* Smith [1967]

* Cook-Torrance [1982]

* Neumann [1999]

* Kelemen [2001]

* Implicit [2013]

<br/>

另外，Eric Heitz在[Heitz14]中展示了Smith几何阴影函数是正确且更准确的G项，并将其拓展为Smith联合遮蔽阴影函数（Smith Joint Masking-Shadowing Function），该函数具有四种形式：

-   分离遮蔽阴影型（Separable Masking and Shadowing）

-   高度相关掩蔽阴影型（Height-Correlated Masking and Shadowing）

-   方向相关掩蔽阴影型（Direction-Correlated Masking and Shadowing）

-   高度-方向相关掩蔽阴影型（Height-Direction-Correlated Masking and Shadowing）

<br/>

目前较为常用的是其中最为简单的形式，分离遮蔽阴影（Separable Masking and Shadowing Function）。

该形式将几何项G分为两个独立的部分：光线方向（light）和视线方向（view），并对两者用相同的分布函数来描述。根据这种思想，结合法线分布函数（NDF）与Smith几何阴影函数，于是有了以下新的Smith几何项：

-   Smith-GGX

-   Smith-Beckmann

-   Smith-Schlick

-   Schlick-Beckmann

-   Schlick-GGX

<br/>

目前在图形学中广受推崇的是 Smith 阴影-遮掩函数. Smith 函数比 Cook-Torrance 函数更加 精确，而且考虑进了表面粗糙度和法线分布的影响. Disney 的BRDF模型中,就是用了Smith-GGX 阴影遮掩函数. 公式如下:

$$G(l,v,h) = \frac{2}{1+\sqrt{1+\alpha_g^2tan\theta_v^2}}$$

> $\alpha_g = (0.5+\frac{roughness}{2})^2$
>
> $θ_v$ 表示观察方向 $v$ 和表面法线 $n$ 之间的夹角.

<br/>

UE4使用的是 Schlick-GGX,即基于Schlick近似,将k映射为 $k = \frac{\alpha}{2}$, 去匹配GGX Smith方程:

$$G(l,v,h) = \frac{(n\cdot l)}{(n\cdot l)(1-k)+k}\frac{(n\cdot v)}{(n\cdot v)(1-k)+k}$$

> $k = \frac{\alpha}{2} $
>   
> $\alpha = roughness^2$

<br/>

Unity5.3 以后使用的阴影遮掩函数是: 

$$lambda_v = (-1+\sqrt{\alpha^2*\frac{(1-n\cdot l)}{n\cdot l}+1}*0.5$$

$$lambda_l = (-1+\sqrt{\alpha^2*\frac{(1-n\cdot l)}{n\cdot l}+1}*0.5$$

$$G = \frac{1}{1+lambert_v+lambert_l}$$

并且由于性能考虑,用乘法简化了开根号的运算,最终公式为:

$$lambda_v = (n\cdot l)((n\cdot v)(1-\alpha^2)+\alpha^2)$$

$$lambda_l = (n\cdot v)((n\cdot l)(1-\alpha^2)+\alpha^2)$$

$$G = \frac{0.5}{(lambdaV + lambdaL + 1e-5f)}$$

> $\alpha = roughness^2$
> $lambda_v$表示出射方向上的阴影和遮掩
> $lambda_l$表示入射方向上的阴影和遮掩

<a name="7"></a>

<br/><br/>

---

<br/><br/>

# 1. Custom PBS Shader in Unity

```csharp
Shader "PBR/CustomPBR"
{
    Properties
    {
        _Color ("Color", Color) = (1, 1, 1, 1)
        _MainTex ("Texture", 2D) = "white" {}
        _Glossiness ("Smoothness", Range(0.0, 1.0)) = 0.5
        _SpecularColor ("Specular", Color) = (0.2, 0.2, 0.2)
        _SpecGlossMap ("Specular (RGB) Smoothness (A)", 2D) = "white" {}
        _BumpScale ("Bump Scale", Float) = 1.0
        _BumpMap ("Normal Map", 2D) = "bump" {}
        _EmissionColor ("Color", Color) = (0, 0, 0)
        _EmissionMap ("Emission", 2D) = "white" {}
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        
        Pass
        {
            Tags{"LightMode" = "ForwardBase"}
            
            CGPROGRAM
            #pragma target 3.0
            #pragma vertex vert
            #pragma fragment frag
            #pragma multi_compile_fwdbase
            #pragma multi_compile_fog

            #include "UnityCG.cginc"
            #include "AutoLight.cginc"
            #include "Lighting.cginc"
            
            float4 _Color;
            float4 _SpecularColor;
            float4 _EmissionColor;
            sampler2D _MainTex; float4 _MainTex_ST;
            sampler2D _SpecGlossMap; float _SpecGlossMap_ST;
            sampler2D _BumpMap; float _BumpMap_ST;
            sampler2D _EmissionMap; float _EmissionMap_ST;
            float _Glossiness;
            float _BumpScale;
            
            
            struct a2v
            {
                float4 vertex:POSITION;
                float4 texcoord:TEXCOORD0;
                float3 normal: NORMAL;
                float4 tangent:TANGENT;
                
            };
            
            struct v2f
            {
                float4 pos : SV_POSITION;
                float2 uv : TEXCOORD0;
                float4 TtoW0 : TEXCOORD1;
                float4 TtoW1 : TEXCOORD2;
                float4 TtoW2 : TEXCOORD3; 
                SHADOW_COORDS(4)
                UNITY_FOG_COORDS(5) // Defined in UnityCG.cginc
            };


            v2f vert (a2v v)
            {
                v2f o;
                UNITY_INITIALIZE_OUTPUT(v2f,o); // Defined in HLSLSupport.cginc
                o.pos = UnityObjectToClipPos(v.vertex); // Defined in UnityCG.cginc
                o.uv = TRANSFORM_TEX(v.texcoord, _MainTex);// Defined in UnityCG.cginc
                
                // Calculate the world vertex position
                float3 worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                // Calculate the world normal position
                fixed3 worldNormal = UnityObjectToWorldNormal(v.normal);
                // Calculate the world tangent position
                fixed3 worldTangent = UnityObjectToWorldDir(v.tangent.xyz);
                // Calculate the world Binormal position
                fixed3 worldBinormal = cross(worldNormal,worldTangent) * v.tangent.w;
                
                o.TtoW0 = float4(worldTangent.x,worldBinormal.x,worldNormal.x,worldPos.x);
                o.TtoW1 = float4(worldTangent.y,worldBinormal.y,worldNormal.y,worldPos.y);
                o.TtoW2 = float4(worldTangent.z,worldBinormal.z,worldNormal.z,worldPos.z);
                
                TRANSFER_SHADOW(o); // Defined in AutoLight.cginc
                UNITY_TRANSFER_FOG(o,o.vertex);
                return o;
            }
            
             inline half3 CustomDisneyDiffuseTerm(half NdotV, half NdotL, half LdotH, half roughness, half3 baseColor)
            {
                half fd90 = 0.5 + 2 * LdotH * LdotH * roughness;
                
                // two fresnel term
                half lightScatter = (1 + (fd90 - 1) * pow(1 - NdotL, 5));
                half viewScatter = (1 + (fd90 - 1) * pow(1 - NdotV, 5));
                
                // UNITY_INV_PI defined at UnityCG.cginc 
                return baseColor * UNITY_INV_PI * lightScatter * viewScatter;
            }
            
            inline half CustomSmithJointGGXVisibilityTerm(half NdotL, half NdotV, half roughness)
            {
                half a2 = roughness * roughness;
                
                // Original Formulation
                // lambda_v = (-1 + sqrt(a2*(1-NdotL2)/NdotL2+1))*0.5f;
                // lambda_l = (-1 + sqrt(a2*(1-NdotV2)/NdotV2+1))*0.5f;
                // G = 1/(1+lambda_v+lambda_l);
                
                // Approximation of the above formulation 
                // (simplify the sqrt, not mathematically correct but close enough)
                
                half lambdaV = NdotL * (NdotV * (1 - a2) + a2);
                half lambdaL = NdotV * (NdotL * (1 - a2) + a2);
                
                return 0.5f / (lambdaL + lambdaV - 1e-5f);
            }
            
            inline half CustomGGXTerm(half NdotH, half roughness)
            {
                half a2 = roughness*roughness;
                half d = (NdotH*a2-NdotH)*NdotH+1.0f;
                return UNITY_INV_PI * a2 / (d*d + 1e-7f);
            }
            
            inline half3 CustomFresnelTerm(half3 c, half cosA)
            {
                half t = pow(1-cosA,5);
                return c+(1-c)*t;
            }
            
            inline half3 CustomFresnelLerp(half3 c0,half3 c1, half cosA)
            {
                half t = pow(1-cosA,5);
                return lerp(c0, c1, t);
            }
            
            

            fixed4 frag (v2f i) : SV_Target
            {
               /* =========== Prepare all the inputs ========== */
               half4 specGloss = tex2D(_SpecGlossMap,i.uv);
               specGloss.a *= _Glossiness;
               half3 specColor = specGloss.rgb * _SpecularColor.rgb;
               half roughness = 1-specGloss.a;
               
               // 计算了入射角的余角的反射颜色,从而得到更好的菲涅尔(Fresnel)反射效果.
               half oneMinusReflectivity = 1-max(max(specColor.r,specColor.g),specColor.b);
               
               half3 diffColor = _Color.rgb * tex2D(_MainTex,i.uv).rgb * oneMinusReflectivity;
               
               fixed4 packedNormal = tex2D(_BumpMap,i.uv);
               // UnpackNormal(packedNormal) = packedNormal.xy*2-1 
               half3 normalTangent =UnpackNormal(packedNormal);
               normalTangent.xy *= _BumpScale;
               normalTangent.z = sqrt(1-saturate(dot(normalTangent.xy,normalTangent.xy)));
               
               // Convert normal from tangent space to worldSpace
               half3 normalWorld = normalize(half3(dot(i.TtoW0.xyz,normalTangent),
                                dot(i.TtoW1,normalTangent),dot(i.TtoW2,normalTangent)));
               
               // TtoW's w component stored the world vertex position
               float3 worldPos = float3(i.TtoW0.w,i.TtoW1.w,i.TtoW2.w);
               
               half3 lightDir = normalize(UnityWorldSpaceLightDir(worldPos));// Defined in UnityCG.cginc
               
               half3 viewDir = normalize(UnityWorldSpaceViewDir(worldPos));// Defined in UnityCG.cginc
               
               half3 reflDir = reflect(-viewDir,normalWorld);
               
               UNITY_LIGHT_ATTENUATION(atten, i, worldPos); // Defined in AutoLight.cginc
               
               /* =========== Compute BRDF terms ========== */
               half3 halfDir = normalize(lightDir+viewDir);
               // saturate to avoid backlight illumination
               half nv = saturate(dot(normalWorld,viewDir));
               half nl = saturate(dot(normalWorld,lightDir));
               half nh = saturate(dot(normalWorld, halfDir));
               half lv = saturate(dot(lightDir,viewDir));
               half lh = saturate(dot(lightDir, halfDir));
               
               // Diffuse term
               half3 diffuseTerm = CustomDisneyDiffuseTerm(nv,nl,lh,roughness,diffColor); 
               
               // Specular Term
               // 可见项 V, 它计算的是阴影-遮掩函数除以高光反射项的分母部分后的结果 
               half V = CustomSmithJointGGXVisibilityTerm(nl,nv,roughness);
               half D = CustomGGXTerm(nh,roughness*roughness);
               half3 F = CustomFresnelTerm(specColor,lh);
               half3 specularTerm = F * V * D;
               
               // Emission Term
               half3 emissionTerm = tex2D(_EmissionMap,i.uv).rgb * _EmissionColor.rgb;
               
               // IBL (Calculate the lighting based on the image for more realistic lighting )
               // IBL 部分的主要思想是使用材质粗糙度对环境贴图进行LOD采样,
               // 因为粗糙度越大的材质,反射的环境光照应该越模糊, 而这可以通过mipmaps进行采样来模拟得到.
               half perceptualRoughness = roughness * (1.7 - 0.7 * roughness);
               half mip = perceptualRoughness * 6;
               
               half4 envMap = UNITY_SAMPLE_TEXCUBE_LOD(unity_SpecCube0, reflDir, mip);// Defined in HLSLSupport.cginc
               half grazingTerm = saturate((1 - roughness) + (1 - oneMinusReflectivity));
               half surfaceReduction = 1.0 / (roughness*roughness + 1.0);
               half3 indirectSpecular = surfaceReduction * envMap.rgb * CustomFresnelLerp(specColor, grazingTerm, nv);
               
               // Combine All Together
               half3 col = emissionTerm + UNITY_PI*(diffuseTerm + specularTerm) * _LightColor0.rgb * nl * atten + indirectSpecular;
               
               
               UNITY_APPLY_FOG(i.fogCoord, c.rgb); // Defined in UnityCG.cginc
               return half4(col,1);              
            } 
            ENDCG
        }
    }
}

```

---

# References
<br/>
 - Unity Shader入门精要 by 冯乐乐
 - 东拼西凑PBR：PBR基础 by 杨超
 - 基于物理的渲染通识课 by Razor Yhang
 - Physically based rendering by Wikipedia
 - PBR浅析 by Bob Blog
 - 浅墨的游戏编程 by 毛星云
 - 各向异性和各向同性 by 百度百科

>**End --Cheng Gu**

