---
layout: post
title:  "Cross Or Dot"
date:   2018-05-14 14:15:05 +0000
image: /assets/images/Post2.png
---

The Cross Product and the Dot Product are the most common calculations of Vector. In the Game Development, it is necessary to understand both of them. The biggest difference between them is that:



1. The result of dot product is a **value**
2. The result of cross product is a **vector**


---
#### 1. Dot Product

{: .center}
![dot](/assets/images/PostImages/Dot.jpg){:height="50%" width="50%"}

The position relation of two vectors can be determined by the sign of the dot product of these two vector.
1. If the dot product = 0, these two vectors are perpendicular. Two Vector point mostly same direction.
2. If the dot product <0, the angle between two Vector is 90 ~ 180. Two Vector perpendicular.
3. If the dot product >0, the angle between two Vector is 0 ~ 90. Two Vector point mostly opposite direction.

***Example in Game Development:***

(1) Get the Angle of two vectors: range [0, 180], whether the view of the game monster can be viewed to the player, and can be used to calculate whether the enemy is within the range of the character's attack.
{% highlight csharp %} 
Vector A,B;
float dotValue = Vector3.Dot (A.normalized, B.normalized);
float angle = Mathf.Acos(dotValue) * Mathf.Rad2Deg;  
//However. there is a easier way to do it in unity by using Vector.Angle().
//float angle= Vector3.Angle (Vector3 from, Vector3 to) 
{% endhighlight %} 
(2) Determine if the target is in front of you, or behind you.
{% highlight csharp %} 
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
{% endhighlight %}
(3) Simulate the flight status of the aircraft
{% highlight csharp %} 
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
{% endhighlight %}



---
#### 2. Cross Product 

{: .center}
![dot](/assets/images/PostImages/Cross.jpg){:height="70%" width="70%"}

The cross product of two vectors, the result of the cross product is a vector instead of a value. Also, the cross product of two vectors is ***perpendicular*** to the coordinate plane of these two vectors.

> In Math, it satisfy right hand rule, a x b = -b x a. So we can use the result of Cross product's value is positive or negative to determine a and b's relative position.

***Right-hand Cooridinate System***

![dot](/assets/images/PostImages/right-hand.jpg){:height="20%" width="20%"}

***Left-hand Cooridinate System***

![dot](/assets/images/PostImages/left-hand.jpg){:height="20%" width="20%"}

Normal vector of a plane is calculated from the Cross Product of the coordinate plane of x and y vectors.

![dot](/assets/images/PostImages/Cross2.jpg){:height="20%" width="20%"}


***Example in Game Development:***

1. WallWalking
2. Determine the target is on your right or left:

{% highlight csharp %} 
// if return a negative value, the target is on the left,
// if return a positive value, the target is on the right,
// if return 0, the the target is on the front side or back side
Vector3.Cross(transform.forward, target.position).y
{% endhighlight %}




---
In conclusion, ***Dot Product*** determine the angle of two Vectors, ***Cross Product*** determine the direction of two Vectors.

For instance, when an enemy behind you, ***Cross Product*** determine whether you should turn left or turn right to face to the enemy quickly; the ***Dot Product*** can get the angle size you need to turn towards the enemy.

>**End --Cheng Gu**

