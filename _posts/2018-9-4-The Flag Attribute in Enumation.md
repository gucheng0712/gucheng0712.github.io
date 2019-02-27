---
layout: post
title:  "The Flag Attribute in Enumation"
date:   2018-09-04 
categories: [Csharp]
tags: [Csharp]
icon: fa-code
---

The problem with enumation is when you want to use conditional operator in your statement, you have to use bit operator.

For example, you can't use `&&` or `||` operator, you have to use bit operator such as `&` or `|`.

>Bit Operator

| Operation | Description |
| `a & b` | if a = b = 1, the result is 1, else is 0 |
| `a | b` | if a and b as long as one of them is 1 then result is 1 |
| `~a` | if a = 1, the result = 0; if a = 0, the result = 1 |
| `a ^ b` | Only when a = b, the result is 0; if a != b, the result is 1 |

---
#### 1. Normal way to use enum in C#

if we want to create a new List Type. Ignore all other details, only focus on the iterator. This List will store the values in array, and this list can set the start point of the iteration. For example, we have 5 elements. We can set start index is 2, the iteration order shoule be 2,3,4,0,1.

>(1) create a enumation

```csharp
    public enum PersonType
    {
        Tall,  
        Rich,   
        Handsome,   
        White,
        Beauty
    }
```

>(2) Declare the enum and use switch case to do condition statement:

```csharp
 public static PersonType person;
 {
        public static void Main(string[] args)
        {
            switch (person)
            {
                case PersonType.Tall:
                    Console.WriteLine("I'm Tall");
                    break;
                case PersonType.Rich:
                    Console.WriteLine("I'm Rich");
                    break;
                case PersonType.Handsome:
                    Console.WriteLine("I'm Handsome");
                    break;
                case PersonType.White:
                    Console.WriteLine("I'm White");
                    break;
                case PersonType.Beauty:
                    Console.WriteLine("I'm Beauty");
                    break;
                default:
                    break;
            }
        }
```

---
#### 2. Problem

> so far, it's fine. However if you want to have multiply choice such as 

```csharp
    case PersonType.White | PersonType.Beauty:
```

> It is still fine. No error at all. Then we try one more choice:

```csharp
    case PersonType.White | PersonType.Handsome:
```

{: style="color: red"}
> So now you will see an error! Why it will happen? 

---
#### 3. Solution


>Because when we create an enum the default value of it is 

```csharp
    public enum PersonType
    {
        Tall = 0,       // 000000
        Rich = 1,       // 000001
        Handsome = 2,   // 000010   
        White = 3,      // 000011
        Beauty = 4      // 000100
    }
```

{: style="color: red"}
>So when the condition is `PersonType.White | PersonType.Beauty`,   the comparison actually is `000011 | 000100 -> 000111`,  there is no other duplicated address, so there is no error.

>However, when it comes to the comparison between `PersonType.White | PersonType.Handsome`,  which actually is `000011 | 000010  -> 000011`.  `000011`  already has another value which is  `PersonType.White`,  so it will give an error.

>So, how can we solve this problem?

>The right way is add a `[Flag]` Attributes, And Change the value to 2^n, so that they will never meet each other

```csharp
    public enum PersonType
    {
        Tall = 1,       // 000001
        Rich = 2,       // 000010
        Handsome = 4,   // 000100   
        White = 8,      // 001000
        Beauty = 16     // 010000
    }
    
     public static PersonType person;
     {
        public static void Main(string[] args)
        {
            switch (person)
            {
                case PersonType.Tall:
                    Console.WriteLine("I'm Tall");
                    break;
                case PersonType.Rich:
                    Console.WriteLine("I'm Rich");
                    break;
                case PersonType.Handsome:
                    Console.WriteLine("I'm Handsome");
                    break;
                case PersonType.White:
                    Console.WriteLine("I'm White");
                    break;
                case PersonType.Beauty:
                    Console.WriteLine("I'm Beauty");
                    break;
                    
                // 001000 | 010000 -> 011000
                case PersonType.White | PersonType.Beauty: 
                    Console.WriteLine("I'm Beauty or white"); 
                    break;
                // 001000 | 000100 -> 001100
                case PersonType.White | PersonType.Handsome:
                    Console.WriteLine("I'm white or handsome");
                    break;
                default:
                    break;
            }
        }
```

---

>**End --Cheng Gu**

