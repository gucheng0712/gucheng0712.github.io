---
layout: post
title:  "Observer Pattern in Delegate and Non-Delegate"
date:   2018-09-16 1:00:05 +0000
image: /assets/images/post21.png
---

Use observer pattern to implement A cat and few mice. When cat comes all the mices are running away.

---
#### 1. Normal ways (Violates the Open Closed Principle)

![dot](/assets/images/PostImages/Normal.png){:height="50%" width="50%"}

>Code:

{% highlight csharp %} 
    public class Normal : MonoBehaviour
    {
        Cat ws = new Cat();
        MouseA a = new MouseA();
        MouseB b = new MouseB();
        MouseC c = new MouseC();

        private void Update()
        {
            if (Input.GetKeyDown(KeyCode.Space))
            {
                ws.CatComing(a, b, c);
            }
        }
    }
    
==================================Class Division================================
    class Cat
    {
        public void CatComing(MouseA a, MouseB b, MouseC c)
        {
            Debug.Log("Cat is coming");
            a.RunAway();
            b.RunAway();
            c.RunAway();
        }
    }
    
==================================Class Division================================
    class MouseA
    {
        public void RunAway()
        {
            Debug.Log("Mouse A Run away");
        }

    }
    
==================================Class Division================================
    class MouseB
    {
        public void RunAway()
        {
            Debug.Log("Mouse B Run away");
        }

    }
    
==================================Class Division================================
    class MouseC
    {
        public void RunAway()
        {
            Debug.Log("Mouse C Run away");
        }

    }

{% endhighlight %}

---
#### 2. ObserverPattern (Complex but often used in big framework)

![dot](/assets/images/PostImages/OB.png){:height="50%" width="50%"}

>Code:

{% highlight csharp %} 
    public class ObserverPattern : MonoBehaviour
    {
        Cat cat = new Cat();

        void Start()
        {
            MouseA mouseA = new MouseA(cat);
            MouseB mouseB = new MouseB(cat);
            MouseC mouseC = new MouseC(cat);
            cat.AddObserver(mouseA);
            cat.AddObserver(mouseB);
            cat.AddObserver(mouseC);
        }

        private void Update()
        {
            if (Input.GetKeyDown(KeyCode.Space))
            {
                cat.CatComing();
            }
        }

    }
    
==================================Class Division================================
    abstract class Subject
    {
        List<Observer> m_observers = new List<Observer>();

        public void AddObserver(Observer ob)
        {
            m_observers.Add(ob);
        }

        public void RemoveObserver(Observer ob)
        {
            m_observers.Remove(ob);
        }

        public void NotifyObserver()
        {
            foreach (var o in m_observers)
            {
                o.Update();
            }
        }
    }

==================================Class Division================================
    class Cat : Subject
    {
        public void CatComing()
        {
            Debug.Log("Cat is Comming");
            NotifyObserver();
        }
    }

==================================Class Division================================
    abstract class Observer
    {
        public abstract void Update();
    }

==================================Class Division================================
    class MouseA : Observer
    {
        public Cat cat;

        public MouseA(Cat _cat)
        {
            cat = _cat;
        }

        public override void Update()
        {
            RunAway();
        }

        private void RunAway()
        {
            Debug.Log("MouseA is run away");
        }
    }

==================================Class Division================================
    class MouseB : Observer
    {
        public Cat cat;

        public MouseB(Cat _cat)
        {
            cat = _cat;
        }

        public override void Update()
        {
            RunAway();
        }

        private void RunAway()
        {
            Debug.Log("MouseB is run away");
        }
    }
    
==================================Class Division================================
    class MouseC : Observer
    {
        public Cat cat;

        public MouseC(Cat _cat)
        {
            cat = _cat;
        }

        public override void Update()
        {
            RunAway();
        }

        private void RunAway()
        {
            Debug.Log("MouseC is run away");
        }
    }
{% endhighlight %}


[ObserverPattern with multiple Subjects]

---
#### 3. Observer Pattern By using C# Event (Simple and Neat)

![dot](/assets/images/PostImages/OB_Event.png){:height="50%" width="50%"}

>Code:

{% highlight csharp %} 
    public class ObserverPattern_Delegate : MonoBehaviour
    {
        Cat cat = new Cat();
        MouseA mouseA = new MouseA();
        MouseB mouseB = new MouseB();
        MouseC mouseC = new MouseC();


        private void Start()
        {
            cat.CatComeEvent += mouseA.RunAway;
            cat.CatComeEvent += mouseB.RunAway;
            cat.CatComeEvent += mouseC.RunAway;
        }

        private void Update()
        {
            if (Input.GetKeyDown(KeyCode.Space))
            {
                cat.CatComing();
            }
        }
    }

==================================Class Division================================
    class Cat
    {
        public delegate void CatComeEventHandler();
        public event CatComeEventHandler CatComeEvent;

        public void CatComing()
        {
            Debug.Log("Cat is Coming");
            if (CatComeEvent != null)
            {
                CatComeEvent();
            }
        }
    }

==================================Class Division================================
    class MouseA
    {
        public void RunAway()
        {
            Debug.Log("MouseA,Run, Cat is coming");
        }
    }
    
==================================Class Division================================
    class MouseB
    {
        public void RunAway()
        {
            Debug.Log("MouseB,Run, Cat is coming");
        }
    }

==================================Class Division================================
    class MouseC
    {
        public void RunAway()
        {
            Debug.Log("MouseC,Run, Cat is coming");
        }
    }


{% endhighlight %}


---

>**End --Cheng Gu**

