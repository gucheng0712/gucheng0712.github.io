---
layout: post
title:  "Trig In Game World"
date:   2018-05-29  
categories: [Math, Unity, Csharp]
tags: [Unity,Csharp,Math,CG, Game Dev]
icon: fa-calculator
---
In this post mainly talks about two parts of trigonometry in game development. Respectively They are Trignometry Unit Circle vs Unity Unit Circle & ATan(y/x) vs ATan2(y,x)

<p align="center">     
<img src="/static/assets/img/blog/trig.jpg" width="80%">
</p>

---
#### 1. Difference in Math & Game

<p align="center">     
<img src="/static/assets/img/blog/diff_game_math.png" width="80%">
</p>

Due to in game development, the forward direction should be a start point. Just like the Picture, the Trig Unit Circle need to rotate counterclockwise 90 degrees to the Unity's Unit Circle. 

eg: 

```csharp
 public float angleInDegrees;

    void Update()
    {
        Vector3 dir = new Vector3(Mathf.Cos((90-angleInDegrees) * Mathf.Deg2Rad), 0, Mathf.Sin((90-angleInDegrees) * Mathf.Deg2Rad));
        Debug.DrawRay(transform.position, dir * 3, Color.cyan);
    }
```
Also, the difference between sin and cos is 90 degree, which shows that you can simply swap the ***sin*** and ***cos*** to convert from Trig Unit Circle to Game Development Unit Circle:

```csharp
    public float angleInDegrees;

    void Update()
    {
        Vector3 dir = new Vector3(Mathf.Sin(angleInDegrees * Mathf.Deg2Rad), 0, Mathf.Cos(angleInDegrees * Mathf.Deg2Rad));
        Debug.DrawRay(transform.position, dir * 3, Color.cyan);
    }
```

---
#### 2. Trig Functions in CG World

<p align="center">     
<img src="/static/assets/img/blog/functions.png" width="80%">
</p>

{: style="color: red"}
>In game development though, it can happen fairly often that the adjacent side is equal to 0 (e.g. the x coordinate of a vector being 0). Remembering that tan(angle) = opposite/adjacent the potential for a disastrous divide-by-zero error should be clear. So a lot of libraries offer a function called atan2, which lets you specify both the x and y parameters, to avoid the division by ***0*** for you and give an angle in the right quadrant.

---

>**End --Cheng Gu**

