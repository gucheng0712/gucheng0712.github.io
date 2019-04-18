---
layout: post
title:  "A Smart Way to Bind UI Events in Unity"
date:   2019-04-18
categories: [Csharp]
tags: [Csharp]
icon: fa-code
---

When we are doing UI interation with code, we will use events. Here we have a Panel with 3 buttons, we have to give each button a different command to do something when pressing the button.

<p align="center">     
<img src="/static/assets/img/blog/uibtns.jpg" width="50%">
</p>


## 1. Stupid Way to Bind UI Events

Normally, what we do is to bind each button's listener to each event.

>Code:

```csharp
using UnityEngine;
using UnityEngine.UI;

public class MyPanel : MonoBehaviour
{
    [SerializeField] Button btn_1;
    [SerializeField] Button btn_2;
    [SerializeField] Button btn_3;

    // sometimes If there are a lot of hidden buttons, 
    // the interface will load a lot of useless listening, 
    // reducing the efficiency of the game.
    // To solve this problem, place the bind function in OnEnable and unbind it in OnDisable.
    void OnEnable()
    {
        btn_1.onClick.AddListener(OnBtn1Click);
        btn_2.onClick.AddListener(OnBtn2Click);
        btn_3.onClick.AddListener(OnBtn3Click);
    }
    private void OnDisable()
    {
        btn_1.onClick.RemoveListener(OnBtn1Click);
        btn_2.onClick.RemoveListener(OnBtn2Click);
        btn_3.onClick.RemoveListener(OnBtn3Click);
    }

    void OnBtn1Click()
    {
        Debug.Log("Btn1 Clicked");
    }

    void OnBtn2Click()
    {
        Debug.Log("Btn2 Clicked");
    }

    void OnBtn3Click()
    {
        Debug.Log("Btn3 Clicked");
    }
}
```

However, We all should know when doing programming, we have a DRY Principle (Don't Repeat Yourself). 

---
#### 2. Smart Way to Bind UI Events using(Lambda)

> Any problem in computer science can be solved by adding a neat middle layer

We can use Lambda as middle layer and OnBtnClick will just simply become a function with one parameter. 

>Code:

```csharp
using UnityEngine;
using UnityEngine.UI;

public class MyPanel : MonoBehaviour
{
    Button[] btns;

    // sometimes If there are a lot of hidden buttons, 
    // the interface will load a lot of useless listening, 
    // reducing the efficiency of the game.
    // To solve this problem, place the bind function in OnEnable and unbind it in OnDisable.
    void OnEnable()
    {
        btns = GetComponentsInChildren<Button>();

        for (int i = 0; i < btns.Length; i++)
        {
            //Important: We can't just use i as the index of btn, because the The allocated memory is passed along with the callback
            int index =i; 
            btns[i].onClick.AddListener(() => { OnBtnClick(index); });
        }
    }
    private void OnDisable()
    {
        foreach (var btn in btns)
        {
            btn.onClick.RemoveAllListeners();
        }
    }

    void OnBtnClick(int index)
    {
        Debug.Log("Btn " + (int)(index+1) + " Clicked");
    }
}   
```

---

>**End --Cheng Gu**

