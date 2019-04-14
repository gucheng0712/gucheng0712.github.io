---
layout: post
title:  "Refactor If Statement By using Delegate"
date:   2019-04-14
categories: [Csharp]
tags: [Csharp]
icon: fa-code
---

`If statements` will make the code looks very `ugly`, and `violate the open closed principle`. But we can use `Delegate` or (Finite State Machine)`FSM` to make it better. 

In this post, I use simple `Player's Movement States` as an example.

---
#### 1. Using If statement to implement Player Movement States

> Code Example

```csharp
public enum Movement
{
    Idle,Walk,Run,Jump
}

public class PlayerMovement : MonoBehaviour
{
    public Movement movement;

    void Update()
    {
        if(movement == Movement.Idle)
        {
            Debug.Log("Player is in Idle State");
        }else if(movement== Movement.Walk)
        {
            Debug.Log("Player is in Walk State");
        }
        else if (movement == Movement.Run)
        {
            Debug.Log("Player is in Run State");
        }
        else if (movement == Movement.Jump)
        {
            Debug.Log("Player is in Jump State");
        }
    }
}
```

---
#### 2. Using Delegate to implement Player Movement States

> **Code Example**

```csharp
public enum Movement
{
     Idle,Walk,Run,Jump
}

public class PlayerMovement : MonoBehaviour
{
    public Movement movement;
    public delegate void MovementHandler();
    MovementHandler[] handler;

    void Start()
    {
        handler = new MovementHandler[] { Idle, Walk, Run, Jump };
    }

    void Update()
    {
        MovementHandler moveState = handler[(int)movement];
        moveState(); // call delegate
    }

    void Idle()
    {
        Debug.Log("Player is in Idle State");
    }
    void Walk()
    {
        Debug.Log("Player is in Walk State");
    }
    void Run()
    {
        Debug.Log("Player is in Run State");
    }
    void Jump()
    {
        Debug.Log("Player is in Jump State");
    }

}
```


---

>**End --Cheng Gu**

