---
layout: post
title:  "Suzuka Wants to Fly Game - Dev Journal"
date:   2018-05-12
desc: "This is a third person spiderman style game demo."
keywords: "Suzuka, Unity, Spiderman"
categories: [Dev journal, Unity, Csharp]
tags: [Unity]
icon: icon-unity
---

<div style='position:relative; padding-bottom:calc(56.25% + 44px)'><iframe src="//player.bilibili.com/player.html?aid=37162427&cid=65307506&page=1"  frameborder='0' scrolling='no' width='100%' height='100%' style='position:absolute;top:0;left:0;' allowfullscreen></iframe></div> 

<br/><br/>
This is a third person spiderman style game demo. Because many people will have motion sickness when they are playing a game with faster visual change in Virtual Reality.

I used a third person view spiderman style swinging game demo to try to discover a new world in visual reality for reducing this adverse consequences. I also want to use this chance to practice my programming abilities. That's the goal for me to create this project.

In this Project, I my own third person controling system for my first anime Character called Suzuka, with swinging moving, wall walking, battle system, and wallwalking machanics (compatible with HTC Vive). Also, I created a infinite city generator instead of wasting time on level design.

<br/><br/>



---
#### 1. Normal State Movement
Player's movement depends on the input direction. When the player is not moving, the camera is free look, but once player moves, the camera view will look along the player's moving direction.
When player jump or isn't grounded, he will always look at the camera's look direction with a smooth transition. Also, he will gain three abilities: 
1. **Swinging**: Done with Unity's Fixed Joint (In the future, it will be improved by using Configuable Joint)
2. **Bursting**: Done with Unity's AddForce () function.
3. **WallWalking**: Done with Raycasting and Cross Product.

<br/><br/>

---
#### 2. Battle State Movement 
When the player encounter an Enemy, player's state will change to battle state, and the only way to change state back to normal state is to beat the enemy. Of course, player will not be able to jump, which means he also can't swing, burst,or walk onto the wall, but he gains weapons from WeaponManager(so far only a hidden blade and a pistol) . Switch weapon can gain different abilities to against enemy.

<br/><br/>

---
#### 3. Infinity City Generator
Also, I created a infinite city generator instead of using a lot of time on level design. (because Level design is a pain) Done by Raycast from each city to detect if there is another city in the front, right, left, back. and based on the player's distance between the cities to determine "destroy" or "instantiate" Done by object pool.

<br/><br/>

---
#### 4. Enemy AI Behavior Tree
So far the enemy has four mental states:
1. **Chase**
2. **Patrol**
3. **Idle**
4. **Die**


<br/><br/>

Due to the limition of unity's NavMeshAgent, it cannot support the procedural generating map. I used waypoints as the Enemy's movement (in the future maybe using A* Pathfinding Algorithm to achieve this challange). 
I used a Spot light's angle as the enemy's detecting range. When the enemy discover the player, he will chase the player. Attacking while being close to the player. Also, in order to prevent enemy go through the wall, I also used LineCast() method to detect if there is something between the player and the enemy when the enemy is chasing the player. 


---
>**End --Cheng Gu**
