---
layout: post
title:  "Cross Or Dot"
date:   2018-05-20 
desc: "The Cross Product and the Dot Product are the most common calculations of Vector."
keywords: "Math, Dot Product, Cross Product"
categories: [Math, Unity, Csharp]
tags: [Unity,Csharp,Math,CG]
icon: fa-calculator
---

The Cross Product and the Dot Product are the most common calculations of Vector. In the Game Development, it is necessary to understand both of them. The biggest difference between them is that:



1. The result of dot product is a **value**
2. The result of cross product is a **vector**


---
#### 1. Dot Product

<p align="center">     
<img src="/static/assets/img/blog/Dot.jpg" width="50%">
</p>

The position relation of two vectors can be determined by the sign of the dot product of these two vector.
1. If the dot product = 0, these two vectors are perpendicular. Two Vector point mostly same direction.
2. If the dot product <0, the angle between two Vector is 90 ~ 180. Two Vector perpendicular.
3. If the dot product >0, the angle between two Vector is 0 ~ 90. Two Vector point mostly opposite direction.

***Example in Game Development:***

(1) Get the Angle of two vectors: range [0, 180], whether the view of the game monster can be viewed to the player, and can be used to calculate whether the enemy is within the range of the character's attack.

```
Vector A,B;
float dotValue = Vector3.Dot (A.normalized, B.normalized);
float angle = Mathf.Acos(dotValue) * Mathf.Rad2Deg;  
//However. there is a easier way to do it in unity by using Vector.Angle().
//float angle= Vector3.Angle (Vector3 from, Vector3 to) 
```

(2) Determine if the target is in front of you, or behind you.

```
float dotValue=Vector3.Dot(transform.forward, target.position)
if(dotValue>0)
{
    print("Target is in front of you");
}else if(dotValue<0)
{
    print("Target is behind you");
}else{
    print("Target is on the left or right.");
}
```

(3) Simulate the flight status of the aircraft

```
float dotValue = Vector3.Dot(transform.forward, Vector3.up);
if (dotValue < 0)
{
    print("This plane is flying down");
}
else if (dotValue > 0)
{
    print("This plane is flying up");
}
else
{
    print("This plane is flying in parallel ");
}
```



---
#### 2. Cross Product 
<p align="center">     
<img src="/static/assets/img/blog/Cross.jpg" width="50%">
</p>

The cross product of two vectors, the result of the cross product is a vector instead of a value. Also, the cross product of two vectors is ***perpendicular*** to the coordinate plane of these two vectors.

> In Math, it satisfy right hand rule, a x b = -b x a. So we can use the result of Cross product's value is positive or negative to determine a and b's relative position.

***Right-hand Cooridinate System*** 
<img src="/static/assets/img/blog/right-hand.jpg" width="20%">


***Left-hand Cooridinate System***
<img src="/static/assets/img/blog/left-hand.jpg" width="20%">


***Normal vector of a plane is calculated from the Cross Product of the coordinate plane of x and y vectors.***
<img src="/static/assets/img/blog/Cross2.jpg" width="20%">

***Example in Game Development:***

(1) WallWalking:

    > The core is to use cross product cauculate the normal of wall's plane, and ground plane's normal to implement into player's Up direction
    
(2) Determine the target is on your right or left:

    > if return a negative value, the target is on the left,
    if return a positive value, the target is on the right,
    if return 0, the the target is on the front side or back side
    

```Vector3.Cross(transform.forward, target.position).y```


(3) Get the area size of a parallelogram which formed by 2 objects

>`S = |b| * |a| * sin(θ)`

(4) Get the angle between two vectors

```
Vector3 a, b;
float value = Vector3.Cross(a.normalized, b.normalized);
float angle = Mathf.Asin(value) * Mathf.Rad2Deg;
```

(5) Can Determine if two vector is **Parallel** or **Intersecting**.


---
In conclusion, ***Dot Product*** determine the angle of two Vectors, ***Cross Product*** determine the direction of two Vectors.

For instance, when an enemy behind you, ***Cross Product*** determine whether you should turn left or turn right to face to the enemy quickly; the ***Dot Product*** can get the angle size you need to turn towards the enemy.

---
>**End --Cheng Gu**

