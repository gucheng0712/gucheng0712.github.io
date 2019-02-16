---
layout: post
title:  "OPP Design Principles"
date:   2018-07-20
categories: [Csharp,Python,Javascript]
tags: [Unity,Csharp,Math,CG, Game Dev]
icon: fa-code
---

For making our code system more stable and portability, esay to maintain, follow the solid design principle is significant.

---
#### 1. SRP (Single Responsibility Principle)

<p align="center">     
<img src="/static/assets/img/blog/SRP1.png" width="40%">
<img src="/static/assets/img/blog/SRP2.png" width="40%">
</p>



>***The single responsibility principle (SRP)*** states that every module or class should have responsibility over a single part of the functionality provided by the software, which means: "When designing to encapsulate a class, the class should be responsible for one kind of function, and all its services should be narrowly aligned with that responsibility". 



---
#### 2. OCP (Open-Closed Principle)

<p align="center">     
<img src="/static/assets/img/blog/OCP.jpg" width="70%">
</p>

>***The open/closed principle*** states "software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification"; that is such an entity can allow its behaviour to be extended without modifying its source code.

>The name open/closed principle has been used in two ways. Both ways use generalizations (for instance, inheritance or delegate functions) to resolve the apparent dilemma, but the goals, techniques, and results are different.

---

#### 3. LSP (Liskov Substitution Principle)

<p align="center">     
<img src="/static/assets/img/blog/LSP.jpg" width="60%">
</p>

>Substitutability is a principle in object-oriented programming stating that "A subclass must be able to replace the parent class. " 

>For example: There are 3 Classes, Employee, Artist, Programmer. Both Artist and Programmer are the employee, so they should be inherit from Employee Class 


---

#### 4. DIP (Dependence Inversion Principle) 

<p align="center">     
<img src="/static/assets/img/blog/DIP.png" width="60%">
</p>

>The principle states: 

1. High-level modules should not depend on low-level modules. Both should depend on abstractions.

2. Abstractions should not depend on details. Details should depend on abstractions.

---

#### 5. ISP (Interface Segregation Principle)

<p align="center">     
<img src="/static/assets/img/blog/ISP.jpg" width="50%">
</p>

>The interface-segregation principle (ISP) states that: "No client should be forced to depend on methods it does not use. " 

>ISP splits interfaces that are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them. Such shrunken interfaces are also called role interfaces. ISP is intended to keep a system decoupled and thus easier to refactor, change, and redeploy. ISP is one of the five SOLID principles of object-oriented design, similar to the High Cohesion Principle of GRASP.

---

#### 6. LKP (Least Knowledge Principle) 

<p align="center">     
<img src="/static/assets/img/blog/LKP.jpg" width="50%">
</p>


>The principle states:
1. Each unit should have only limited knowledge about other units: only units "closely" related to the current unit.
2. Each unit should only talk to its friends; don't talk to strangers.
3. Only talk to your immediate friends.

---

#### 7. Composition over inheritance 

<p align="center">     
<img src="/static/assets/img/blog/COI.png" width="70%">
</p>

>To favor composition over inheritance is a design principle that gives the design higher flexibility. It is more natural to build business-domain classes out of various components than trying to find commonality between them and creating a family tree

>Initial design is simplified by identifying system object behaviors in separate interfaces instead of creating a hierarchical relationship to distribute behaviors among business-domain classes via inheritance. This approach more easily accommodates future requirements changes that would otherwise require a complete restructuring of business-domain classes in the inheritance model. Additionally, it avoids problems often associated with relatively minor changes to an inheritance-based model that includes several generations of classes.


---

>**End --Cheng Gu**

