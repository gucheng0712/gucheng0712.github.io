---
layout: post
title:  "C# Iterator"
date:   2018-08-28 12:15:05 +0000
image: /assets/images/post17.png
---

Iterator can make you get all the elements in a sequence without caring about its type is ***Array***, ***List***, or other sequence structure.  

In .Net, Iterator is encapsulated by ***IEnumerator*** and their corresponding generic interfaces***IEnumerable*** 

In C#1, it has already supported iterator, which is the ***foreach*** statement. Compared with using for loop to, many times using ***foreach*** will be more easy and strightforward. when doing ***foreach***, the compiler will compile ***foreach*** to invoke the ***GetEnumerator*** and ***MoveNext*** methods, as well as the ***Current*** property.  


{: .center}
![dot](/assets/images/PostImages/iterator.png){:height="80%" width="80%"}

---
#### 1. C# 1.0: Implementing Iterators Manually


if we want to create a new List Type. Ignore all other details, only focus on the iterator. This List will store the values in array, and this list can set the start point of the iteration. For example, we have 5 elements. We can set start index is 2, the iteration order shoule be 2,3,4,0,1.

>Main Program:

{% highlight csharp %} 
class MainClass
    {
        public static void Main(string[] args)
        {
            object[] values = { "a", "b", "c", "d", "e" };
            IterationSample collection = new IterationSample(values, 2);
            foreach (object x in collection)
            {
                Console.WriteLine(x);
            }
        }
    }
{% endhighlight %}

>IterationSample Class:

{% highlight csharp %} 
 public class IterationSample : IEnumerable
    {
        public Object[] values;
        public Int32 startingPoint;

        public IterationSample(Object[] values, Int32 startingPoint)
        {
            this.values = values;
            this.startingPoint = startingPoint;
        }

        // In order to use "foreach" statement, this method is necessary
        public IEnumerator GetEnumerator()
        {
            return new IterationSampleEnumerator(this);
        }
    }
{% endhighlight %}

> In above code, we implemented IEnumerable Interface, but we haven't implement the GetEnumerator() method. We can use this ***IterationSample*** class to implement the Ienumerator Interface, and use different functions implements the Iterator's Logics, but this goes against the principle of single responsibility. So we use another class to implement this.

{% highlight csharp %} 
 public class IterationSampleEnumerator : IEnumerator
    {
        IterationSample parent;//object to iterate
        Int32 position;// currentIndex Position
        internal IterationSampleEnumerator(IterationSample parent)
        {
            this.parent = parent;
            position = -1;
        }

        public bool MoveNext()
        {
            //determine the current position if the last index in the list
            if (position != parent.values.Length) 
            {
                position++;
            }
            return position < parent.values.Length;
        }

        public object Current
        {
            get
            {
                //not allow to access the first and the last value
                if (position == -1 || position == parent.values.Length)
                {
                    throw new InvalidOperationException();
                }
                
                // consider the case of custom start position 
                Int32 index = position + parent.startingPoint;
                index = index % parent.values.Length;
                return parent.values[index];
            }
        }

        public void Reset()
        {
            position = -1;
        }
    }
{% endhighlight %}


---
#### 2. C# 2.0: Implementing Iterators


After C# 2.0, it introduced ***iterator***, and ***yield return*** statements. It becomes much more simplier.

>Main Program:

{% highlight csharp %} 
class MainClass
    {
        public static void Main(string[] args)
        {
            object[] values = { "a", "b", "c", "d", "e" };
            IterationSample collection = new IterationSample(values, 2);
            foreach (object x in collection)
            {
                Console.WriteLine(x);
            }
        }
    }
{% endhighlight %}

>IterationSample Class:

{% highlight csharp %} 
 public class IterationSample : IEnumerable
    {
        public Object[] values;
        public Int32 startingPoint;

        public IterationSample(Object[] values, Int32 startingPoint)
        {
            this.values = values;
            this.startingPoint = startingPoint;
        }

        // In order to use "foreach" statement, this method is necessary
        public IEnumerator GetEnumerator()
        {
            for (int index = 0; index < this.values.Length; index++)
            {
                yield return values[(index + startingPoint) % values.Length];
            }   
        }
    }
{% endhighlight %}

---

>**End --Cheng Gu**

