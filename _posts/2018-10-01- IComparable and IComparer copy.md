---
layout: post
title:  "IComparable and IComparer"
date:   2018-10-1 1:00:05 +0000
categories: [Csharp]
tags: [Csharp]
icon: fa-code
---

In Collection class, when we are going to use simple Sort() function in a List, the base types all provide default comparison algorithms, such as string that provides the alphabetic comparisions and int that provides integer comparisions.

However, when we create our own class. If we want to sort the certain variable in the object list, the class need to implement the IComparable Interface.


---
#### 1. Sorting Integer and String Lists

If the elements inside the list are strings, it will sort by the first letter of the string:

> Code:

```
   	List<string> strList = new List<string>() { "Tom", "Jerry", "Joab", "Edi", "Kane", "Hans" };
       strList.Sort();
       strList.ForEach(Console.WriteLine);
```

> Output:

```
    Edi
    Hans
    Jerry
    Joab
    Kane
    Tom
```

If the elements inside the list are integers, it will sort by the integer from small to big:

> Code:

```
    List<int> nums = new List<int>() { 2, 33, 22, 1, 5, 8 };
    nums.Sort();
    nums.ForEach(Console.WriteLine);
```

> Output:

```
    1
    2
    5
    8
    22
    33
```

However, if you create a `Human` class, and declare a variable called Name, you want to sort the Human by name, it will give you an error after you using `Sort()` function, if you didn't implement the `IComparable` Interface in the `Human` class.

>Code:

```
    class Human
    {
        public string Name { get; set; }
        public int Age { get; set; }

        public Human(string name, int age)
        {
            Name = name;
            Age = age;
        }
    }
    --------------------
    public static void Main(string[] args)
    {
        List<Human> human = new List<Human>()
        {
            new Human("Tom",22),
            new Human("Jerry",12),
            new Human("Joab",18),
            new Human("Kyroll",11),
            new Human("Cheng Gu",19)
        };

        human.Sort();
        human.ForEach(Console.WriteLine);
    }
```


> Output: 

```
System.Collections.Generic.ArraySortHelper<Human>.Sort(Human[] keys, int index, int length, System.Collections.Generic.ObjectComparer<Human> comparer) in /Users/builder/jenkins/workspace/build-package-osx-mono/2018-02/external/bockbuild/builds/mono-x64/external/corert/src/System.Private.CoreLib/src/System/Collections/Generic/ArraySortHelper.cs:66
```

---
#### 2. IComparable

So Only if you implement the `IComparable` Interface

>So, lets sort Human by name
> Code:

```
    class Human : IComparable<Human>
    {
        public string Name { get; set; }
        public int Age { get; set; }

        public Human(string name, int age)
        {
            Name = name;
            Age = age;
        }

        public int CompareTo(Human other)
        {
            if (Age > other.Age) { return 1; }
            else if (Age < other.Age) { return -1; }
            else { return 0; }

            /* 
             * Below is the default CompareTo() which does the same thing as above, 
             * used here to better illustrate how the comparator works.
             */
            // return Age.CompareTo(other.Age);
        }

        public override string ToString()
        {
            return Name + " " + Age;
        }
    }
    ----------------
    public static void Main(string[] args)
    {
        List<Human> human = new List<Human>()
        {
            new Human("Tom",22),
            new Human("Jerry",12),
            new Human("Joab",18),
            new Human("Kyroll",11),
            new Human("Cheng Gu",19)
        };
        human.Sort();
        human.ForEach(Console.WriteLine);
    }
    
```

> Output:

```
    Kyroll 11
    Jerry 12
    Joab 18
    Cheng Gu 19
    Tom 22
```


---
#### 3. IComparator

Now, The `IComparable` Interface solved the sorting error. There is another question pop up. What if you don't want to Sort by age, you want to sort by human's name, so you can use IComparer to implement a custom comparator.


>Code:

```
    class Human : IComparable<Human>
    {
        public string Name { get; set; }
        public int Age { get; set; }

        public Human(string name, int age)
        {
            Name = name;
            Age = age;
        }

        public int CompareTo(Human other)
        {
            if (Age > other.Age) { return 1; }
            else if (Age < other.Age) { return -1; }
            else { return 0; }

            /* 
             * Below is the default CompareTo() which does the same thing as above, 
             * used here to better illustrate how the comparator works.
             */
            // return Age.CompareTo(other.Age);
        }

        public override string ToString()
        {
            return Name + " " + Age;
        }

    }
    ----------
    class HumanNameComparer : IComparer<Human>
    {
        public int Compare(Human x, Human y)
        {
            return x.Name.CompareTo(y.Name);
        }
    }
    ----------
    class MainClass
    {
        public static void Main(string[] args)
        {
            List<Human> human = new List<Human>()
            {
                new Human("Tom",22),
                new Human("Jerry",12),
                new Human("Joab",18),
                new Human("Kyroll",11),
                new Human("Cheng Gu",19)
            };
            human.Sort(new HumanNameComparer());
            human.ForEach(Console.WriteLine);
```

> Output:

```
    Cheng Gu 19
    Jerry 12
    Joab 18
    Kyroll 11
    Tom 22
```

---

>**End --Cheng Gu**

