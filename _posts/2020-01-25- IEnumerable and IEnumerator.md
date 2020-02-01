---
layout: post
title:  "IEnumerable and IEnumerator"
date:   2020-01-21 1:00:05 +0000
categories: [Csharp]
tags: [Csharp]
icon: fa-code
---

IEnumerable and IEnumerator both are interfaces in C#.

`IEnumerable` is an interface defining a single method `GetEnumerator()` that returns an IEnumerator interface.


`IEnumerator` has two methods `MoveNext()` and `Reset()`. It also has a property called `Current`.

---
#### 1. Foreach and Enumerables

Unlike `for loop`, `foreach loop` is not specifiying the exact index  A foreach statement is basically implemented by IEnumerable and IEnumerator.

A Common for loop statement:

```csharp
    int[] arr = new int[] {1,2,3};
    
    for(int i = 0;i<arr.Length;i++)
    {
        Console.WriteLine(arr[i]);
    }  
```
> Output:

```
   1
   2
   3
```

A Common foreach statement:

```csharp
    int[] arr = new int[] {1,2,3};
    
    foreach(var item in arr)
    {
        Console.WriteLine(item);
    }  
```
> Output:

```
   1
   2
   3
```

With IEnumerable and IEnumerator


```csharp
    int[] arr = new int[] {1,2,3};

    IEnumerable enumerator = arr.GetEnumerator();
    while(enumerator.MoveNext())
    {
        Console.WriteLine(enumerator.Current);
    }
```

> Output:

```
   1
   2
   3
```


---
#### 2. Examples

```csharp
class Demo : IEnumerable, IEnumerator 
{
   // IEnumerable method GetEnumerator()
   IEnumerator IEnumerable.GetEnumerator() 
   {
      throw new NotImplementedException();
   }
   public object Current 
   {
      get { throw new NotImplementedException(); }
   }
   // IEnumertor method
   public bool MoveNext() 
   {
      throw new NotImplementedException();
   }
   // IEnumertor method
   public void Reset() 
   {
      throw new NotImplementedException();
   }
}
```

---

>**End --Cheng Gu**

