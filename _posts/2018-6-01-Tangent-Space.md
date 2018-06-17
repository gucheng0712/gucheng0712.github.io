---
layout: post
title:  "Tangent Space"
date:   2018-06-01 14:15:05 +0000
image: /assets/images/post6.jpg
---

Tangent Space is one of the important coordinate in CG. The most important usage of Tangent Space is ***normal mapping***.

---
#### 1. What is Tangent Space

{: .center}
![dot](/assets/images/PostImages/tangent space.png){:height="50%" width="50%"}

>Intuitively, the texture coordinates in a model's vertices are defined in the tangent space.Ordinary, 2-dimensional texture coordinates include ***U*** and ***V***. The direction of U grows is the tangent axis; the direction of V grows is the binormal(also called bitangent) axis. The coordinate system which composed of the ***Tangent***, ***Binormal***, and ***Normal*** is the ***Tangent Space***

---
#### 2. Calculating Tangent Space

{: .center}
![dot](/assets/images/PostImages/tangent space2.png){:height="50%" width="50%"}

This picture showed a trangle and its tangent space. and it have three vertices ***P0, P1, P2. E0 = P1 - P0, E1 = P2 - P0***, so the corresponding texture coordinate difference is: ***(T1, B1) = (U1-U0, V1-V0), (T2, B2) = (U2,U0,V2,V0)***. 

From here we can get: 
>E0 = T1 x T + B1 x B

>E1 = T2 x T + B2 x B

So that we can write it as a Matrix:

![dot](/assets/images/PostImages/tangent space_matrix.png){:height="20%" width="20%"}

Then, break them down into components:

![dot](/assets/images/PostImages/tangent space_matrix2.png){:height="30%" width="30%"}

Transformation: 

![dot](/assets/images/PostImages/tangent space_matrix4.png){:height="30%" width="30%"}

Based on Matrix formula, when there is a matrix ![dot](/assets/images/PostImages/tangent space_matrix5.png){:height="10%" width="10%"}, we can get its Inverse Matrix:
![dot](/assets/images/PostImages/tangent space_matrix6.png){:height="20%" width="20%"}

Therefore, the above formular can be further expressed as:

![dot](/assets/images/PostImages/tangent space_matrix7.png){:height="40%" width="40%"}

At this point, the data on the right side of the equal sign are all known, so the matrix on the left can be easy to calculate, and obtain the T and B Axis in the tangent space. The N-Axis, which is the normal to the trangular surface, it can be easily calculate by using Cross Product of Tangent Axis, and Binormal Axis. 

> There is one thing important in this calculation: the result of T vector and B vector were not normalized, which means the length is not equal. Also for the case of texture coordinate transformation, the T and B axis are not perpendicular to each other.

> However, in most cases, we only need to normalize the T,B,N vectors without worrying about their coorresponding length. For example, in normal mapping, we use the TBN coordinated system only for the purpose of converting the normals from tangent space to world space without any relation to the texture coordinates. Therefore, here I use three of the TBN coordinates are all ready to go.  

---

>**End --Cheng Gu**

