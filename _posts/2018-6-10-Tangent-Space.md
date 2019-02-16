---
layout: post
title:  "Tangent Space"
date:   2018-06-10 14:15:05 +0000
desc: "In this post, I focus on reviewing the Lighting Models."
keywords: "math, normal, tangent, lighting, shader"
categories: [Cg, Math]
tags: [Unity, Shader, Math, CG]
icon: icon-splatter
---

Tangent Space is one of the important coordinate in CG. The most important usage of Tangent Space is ***normal mapping***.

---
#### 1. What is Tangent Space

<p align="center">     
<img src="/static/assets/img/blog/tangent space.png" width="50%">
</p>

>Intuitively, the texture coordinates in a model's vertices are defined in the tangent space.Ordinary, 2-dimensional texture coordinates include ***U*** and ***V***. The direction of U grows is the tangent axis; the direction of V grows is the binormal(also called bitangent) axis. The coordinate system which composed of the ***Tangent***, ***Binormal***, and ***Normal*** is the ***Tangent Space***

---
#### 2. Calculating Tangent Space

<p align="center">     
<img src="/static/assets/img/blog/tangent space2.png" width="50%">
</p>

This picture showed a trangle and its tangent space. and it have three vertices ***P0, P1, P2. E0 = P1 - P0, E1 = P2 - P0***, so the corresponding texture coordinate difference is: ***(T1, B1) = (U1-U0, V1-V0), (T2, B2) = (U2-U0,V2-V0)***. 

From here we can get: 
>E0 = T1 x T + B1 x B

>E1 = T2 x T + B2 x B

So that we can write it as a Matrix:

<img src="/static/assets/img/blog/tangent space_matrix.png" width="20%">

Then, break them down into components:

<img src="/static/assets/img/blog/tangent space_matrix2.png" width="30%">

Two Sides multiply by the inverse of TB Matrix: 

<img src="/static/assets/img/blog/tangent space_matrix4.png" width="30%">

Based on Matrix formula, when there is a matrix <img src="/static/assets/img/blog/tangent space_matrix5.png" width="10%">, we can get its Inverse Matrix:
<img src="/static/assets/img/blog/tangent space_matrix6.png" width="20%">

Therefore, the above formular can be further expressed as:

<img src="/static/assets/img/blog/tangent space_matrix7.png" width="40%">

At this point, the data on the right side of the equal sign are all known, so the matrix on the left can be easy to calculate, and obtain the T and B Axis in the tangent space. The N-Axis, which is the normal to the trangular surface, it can be easily calculate by using Cross Product of Tangent Axis, and Binormal Axis. 

> There is one thing important in this calculation: the result of T vector and B vector were not normalized, which means the length is not equal. Also for the case of texture coordinate transformation, the T and B axis are not perpendicular to each other.

> However, in most cases, we only need to normalize the T,B,N vectors without worrying about their coorresponding length. For example, in normal mapping, we use the TBN coordinated system only for the purpose of converting the normals from tangent space to world space without any relation to the texture coordinates. Therefore, here I use three of the TBN coordinates are all ready to go.  

---

>**End --Cheng Gu**

