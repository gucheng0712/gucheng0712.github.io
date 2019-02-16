---
layout: post
title:  "Unity Rendering Path"
date:   2018-08-14
categories: [Unity, Cg]
tags: [Unity, CG]
icon: icon-splatter
---

Unity support many kinds of rendering path. In Unity 5.0, there are three types of rendering path: forward, deferred, and vertex lit rendering path.

---
#### 1. Forward Rendering Path

<p align="center">     
<img src="/static/assets/img/blog/fwdRendering.png" width="80%">
</p>

For each forward rendering, we need to render the rendering primitive of the object and calculate the data of two buffers (color buffer and depth buffer). We use the depth buffer to determine if a fragment is visible and update the color value of the color buffer if it is visible.

>We can use pseudo code to describe the process of forward rendering:

```
Pass
{
    for(each primitive in this model)
    {
        for(each fragment covered by this primitive)
        {
            if(failed in depth test)
            {
                discard;
            }
            else
            {
                float4 color = Shading(materialInfo, pos, normal, lightDir, viewDir);

                WriteFrameBuffer(fragment, color);
            }
        }
    }
} 
```

---
#### 2. Deferred Rendering Path

<p align="center">     
<img src="/static/assets/img/blog/deferredrendering.png" width="80%">
</p>

Deferred Rendering mainly includes two Pass. The first Pass, we don’t do any lighting calculations, and only calculate which fragment in the screen is visible, by depth buffer. After finding a fragment is visible, it will store the data into the G-Buffer. Then, in the second Pass, it will use the data of fragment in the G-Buffer to do the lighting calculation. 

>We can use pseudo code to describe the process of the deferred rendering:

```
PassOne // Store fragment data into G-Buffer
{
    for(each primitive in this model)
    {
        for(each fragment covered by this primitive)
        {
            if(failed in depth test)
            {
                discard;
            }
            else
            {
                WriteGBuffer(materialInfo, pos, normal, lightDir, viewDir);
            }
        }
    }
}
PassTwo // Calcaulate Lighting by using the data in G-Buffer
{
    for(each pixel in the screen)
    {
        if(the pixel is valid)
        {
            readGBuffer(pixel, materialInfo, pos, normal, lightDir, viewDir);
            float4 color = Shading(materialInfo, pos, normal, lightDir, viewDir);
            WriteFrameBuffer(pixel, color);
        }
    }
} 
```

---
#### 2. Why use Deferred Rendering

>Normally we will use Forward Rendering, but when the scene has too many light sources, the performance of Forward Rendering drops dramatically. 

>For example, if we place multiple light sources in an area in the Unity Scene, and these light source areas overlap and influence with each other. In order to get the final lighting effects, we need to perform multiple Pass for each object in the area to calculate the lighting result of different light source to this object, finally, blend these results in the color buffer to get the final lighting. Therefore, every time when we call a Pass we need to render the object again, but many calculations actually is repeat. 

{: style="color: red"}
>Deferred Rendering can solve this problem because it not only uses color buffer, and depth buffer, it also use an additional buffer called G-Buffer (Geometry- Buffer). G-Buffer stores the information of the surface we care (normally it means the surface which is the closest to camera), such as the surface’s Normal, Position, Material Properties used for illumination calculation, etc.


---

>**End --Cheng Gu**

