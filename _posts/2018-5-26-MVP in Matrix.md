---
layout: post
title:  "Matrix MVP"
date:   2018-05-26
categories: [Math, Unity, Csharp]
tags: [Unity,Csharp,Math,CG]
icon: fa-calculator
---

<p align="center">  
<img src="/static/assets/img/blog/mvp_transformation.png" width="90%">
</p>

MVP Matrix is the shorthand of  ***Model***,  ***View*** and ***Projection*** matrices. Model matrix represents the 3d model's transformation from object space to world space. View matrix represents the transformation of the 3d model from the world space to the camera(view) space. Projection(Clip) matrix represents the transformation of the 3d model from camera space to clip(screen) space.


---
## Model
Model matrix is used for the transformation of 3D Models from model space(also called object/local space) to the world space. It includes Translation, Rotation, and Scale. For more Detail see post [Matrix SRT](https://gucheng0712.github.io/math/unity/csharp/2018/05/26/SRT-in-Matrix.html)

---
## View

View matrix is used for the transformation of 3D Models from world space to view(camera) space. This is called **view transformation**. As we all known, there are two major coordinate system, right hand coordinate system, and left hand coordinate system. In Unity game engine, it uses left hand coordinate system, but in the view space, it will be coverted to left hand coordinate system.

<p align="center">  
<img src="/static/assets/img/blog/camera_space.png" width="40%">
</p>

In order to get the model's position in view space, we can place the origin of the camera at the origin of the world coordinate. based on the Transform Component in above picture, the transformation of camera in world space order is rotate (30,0,0), then translate(0,10,-10). so in order to move the camera's position back to the world origin, we need to do the inverse transformation, first translate(0,-10,10) then rotate(-30,0,0). we can get a matrix $M_{view^\prime}$

\begin{equation}
    M_{view^\prime} = \begin{bmatrix} 
        1 & 0 & 0 & 0 \\\
        0 & cos(-30) & -sin(-30) & 0 \\\
        0 & sin(-30) & cos(-30) & 0 \\\
        0 & 0 & 0 & 1
    \end{bmatrix} 
    \begin{bmatrix} 
        1 & 0 & 0 & 0 \\\
        0 & 1 & 0 & -10 \\\
        0 & 0 & 1 & 10 \\\
        0 & 0 & 0 & 1
    \end{bmatrix} 
\end{equation}

However, due to the view space's coordinate system is changed to right hand coordinate system , so we have to invert the z component by mulitply another matrix $M_{negatez}$

\begin{equation}
    M_{view} = M_{negatez}M_{view^\prime} =  
    \begin{bmatrix} 
        1 & 0 & 0 & 0 \\\
        0 & 1 & 0 & 0 \\\
        0 & 0 & -1 & 0 \\\
        0 & 0 & 0 & 1
    \end{bmatrix} 
    M_{view^\prime}
\end{equation}

Finally, we can use this $M_{view}$ muiltiply the 3d model's world position $P_{world} = (9,4,18.072,1)$ to get the 3d model's position in view space $P_{view}$.

\begin{equation}
    P_{view}^T = M_{view}P_{world}^T = 
    M_{view}
    \begin{bmatrix} 
        9 \\\
        4 \\\
        18.072 \\\
        1 
    \end{bmatrix} 
\end{equation}

$$P_{view} = (9, 4,18.072, 1)$$

<br>
<br>

---
## Projection

Projection matrix is also called clip matrix which is used for the transformation of 3D Models from view space to clip space, then perform standard homogeneous division, also known as perspective division to get the Normalized Device Coordinates(NDC). Finally project on to screen which is known as screen space. Different from view space, the screen space is a 2-Dimensional space.

The goal of clip space is to clip the render objects based on the **view frustum**. everything inside this view frustum will be rendered onto screen, others will be clipped. There are 2 types of Projection, one is Perspective projection, another is orthographic projection(shown in below image).

<p align="center">  
<img src="/static/assets/img/blog/camera_projection.png" width="70%">
</p>

---

### 1. Perspective Projection

Perspective projection is characterized by the near large far small. There are 4 input parameters in perspective projection, Field of View(FOV), Aspect, Near , and Far. 

<p align="center">  
<img src="/static/assets/img/blog/perspective_projection_frustum.png" width="30%">
</p>

The purpose of perspective projection is to transform the view frustum into a cuboid for eaier clipping. After the transformation, the upper right corner of the near clipping plane of the view frustum will become the center of the front plane of the cuboid. This is known as Normalized Device Coordinates(NDC)

As can be seen from below figure, the transformation process is to enlarge the smaller part and shrink the larger part to form the final cuboid. That's why the projection transformation is going to have a smaller effect.The x-coordinate range after transformation is [-1, 1], the y-coordinate range is [-1, 1], and the z-coordinate range is [-1, 1] (DirectX is slightly different, and the z-value range is [0, 1]). 

<p align="center">  
<img src="/static/assets/img/blog/perspectivet_ransformation.png" width="50%">
</p> 

Based on above infomation, we can get the perpective projection matrix $M_{frustum}$.

\begin{equation}
    M_{persp} = 
    \begin{bmatrix} 
      \frac{cot\frac{FOV}{2}}{Aspect} & 0 & 0 & 0 \\\
      0 & cot\frac{FOV}{2} & 0 & 0 \\\
      0 & 0 & -\frac{Far+Near}{Far-Near} & -\frac{2\cdot Near\cdot Far}{Far-Near} \\\
      0 & 0 & -1 & 0
    \end{bmatrix} 
\end{equation}

Then we can get $P_{clip}$ by multiply $P_{view}$.

$$P_{clip} = M_{persp}P_{view}$$

For the derivation method, refer to the this [Link](https://www.cnblogs.com/graphics/archive/2012/07/25/2582119.html)

---

### 2. Orthographic Projection

The view frustum of orthographic projection is a cuboid, so it will be easier to calculate the transformation matrix, M_{ortho}:

\begin{equation}
M_{ortho} = 
    \begin{bmatrix} 
      \frac{1}{Aspect\cdot Size} & 0 & 0 & 0 \\\
      0 & \frac{1}{Size} & 0 & 0 \\\
      0 & 0 & -\frac{2}{Far-Near} & -\frac{Near+Far}{Far-Near} \\\
      0 & 0 & 0 & 1
    \end{bmatrix} 
\end{equation}

Then we can get $P_{clip}$ by multiply $P_{view}$.

$$P_{clip} = M_{ortho}P_{view}$$

Similarly, we need to scale the orthogonal projection matrix's x,y,z components to the range of [-1, 1](In DirectX, z component's range is [0, 1]) become Normalized Device Coordinates(NDC).

<p align="center">  
<img src="/static/assets/img/blog/orthographic_matrix.png" width="70%">
</p> 


---

>**End --Cheng Gu**

