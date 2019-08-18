---
layout: post
title:  "C# Reflection in Unity"
date:   2019-04-19
categories: [Unity,Csharp]
tags: [Unity,Csharp]
icon: fa-code
---

# **What is Reflection?**

`Reflection` is an important mechanism in `C#` that allows to `get information` about the `members` and every member's `type`(class, structure, delegate, interface, enum, etc.) from a `program` or `assembly`(dll, exe) at `runtime`. 

> `Pros`: Reflection improves the `flexibility` and `extensibility` of the program, `reduces the coupling` and `improves the adaptability`. It allows programs to create and control objects of any class without hard-coding the target class in advance.

> `Cons`: Reflection is very `slow`, and make the internal logic of the program more `complex`, and hard to `read` and `maintain`.

---

# **System.Type Class**

The System.Type class plays a `central role` in reflection. It represents the `instance of the target Class`, and provides all the methods of `getting the type` information from the target class.

## **1. Get a Type reference for a given Type**

1. Using `typeof()` operation:
    ```python
    Type t = typeof(string);
    ```
2. Using `GetType()` method:
    ```python
    string s = "grayworm";
    Type t = s.GetType(); 
    ```
---

## **2. Type Class's Properties**

* `Type.Name:` Get the Type's name
* `FullName:` Get full names of the Type(include namespace)
* `Namespace:` Get the NameSpace name
* `IsAbstract:` Get a bool shows if this type is an abstract type
* `IsArray: ` Get a bool shows if this type is an array
* `IsClass:` Get a bool shows if this type is a class  
* `IsEnum:` Get a bool shows if this type is an enum
* `IsInterface:` Get a bool shows if this type is an interface
* `IsPublic:` Get a bool shows if this type is public
* `IsSealed:` Get a bool shows if this type is sealed
* `IsValueType:` Get a bool shows if this type is value type, if not then it's reference type

---

## **3. Type Class's Methods**

> After `Getting the Type` of the Object through the following code:

```csharp
    // Create a new instance of the TargetClass
    TargetClass tc = new TargetClass();

    // Get the type of TargetClass
    Type t = tc.GetType();
```

> Then it will be able to call the `Type` class's `methods` to get the information.

* `GetField(), GetFields()：` Return a `FieldInfo` Type, for getting the class Member variables' information.
```csharp
    // Get the Fields from the TargetClass
    FieldInfo[] fis = t.GetFields();
    foreach (FieldInfo fi in fis)
    {
        Debug.Log(fi.Name);
    } 
```
* `GetMethod(), GetMethods()：` Return a `MethodInfo` Type, for getting the methods' information of the class.
```csharp
    // Get the Methods from the TargetClass
    MethodInfo[] mis = t.GetMethods();
    foreach (MethodInfo mi in mis)
    {
        Debug.Log(mi.ReturnType + " " + mi.Name);
    }
```
* `GetProperty(), GetProperties()：` Return a `PropertyInfo` Type, for getting the Properties' information of the class.
```csharp
    // Get the Properties from the TargetClass
    PropertyInfo[] pis = t.GetProperties();
    foreach (PropertyInfo pi in pis)
    {
        Debug.Log(pi.Name);
    }
```
* `GetConstructor(), GetConstructors()：` Return a `ConstructorInfo` Type, used for get the class's Constructors's information.
```csharp
    // Get all the Constructors of the TargetClass
    ConstructorInfo[] constructorInfos = t.GetConstructors();   

    foreach (ConstructorInfo info in constructorInfos) 
    {
        // Get all the parameters of the constructor
        ParameterInfo[] parameterInfos = info.GetParameters();    
        foreach (ParameterInfo pi in parameterInfos)
        {
            Debug.Log(pi.ParameterType.ToString() + " " + pi.Name + ",");
        }
    }
```
* `GetEvent(), GetEvents()：` Return a `EventInfo` Type, for getting the class's event's information.
```csharp
    // Get the Events from the TargetClass
    EventInfo[] mis = t.GetEvents();
    foreach (EventInfo mi in mis)
    {
        Debug.Log(mi.EventHandlerType + " " + mi.Name);
    }
```
* `GetMember(), GetMembers()：` Return a `MemberInfo` Type, for getting all the members's information of this class.
```csharp
    // Get the Members from the TargetClass
    MemberInfo[] mis = t.GetMembers();
    foreach (MethodInfo mi in mis)
    {
        Debug.Log(mi.ReturnType + " " + mi.Name);
    }
```

For calling these member, using `Type.InvokeMember()`, or other class's(such as FieldInfo, EvenetInfo, PropertyInfo, etc.) `Invoke()` functions.

---

## **4. BindingFlags**

`BindingFlags` is to Specify flags that control binding and the way in which the search for members and types is conducted by reflection.

>Some Common BindingFlags:

* `BindingFlags.IgnoreCase:` Ignore the Case of the member name
* `BindingFlags.DeclaredOnly:` Only Accept the members declared in this class not include inherited memebers.
* `BindingFlags.Instance:` Only Accept the Instance members
* `BindingFlags.Static:` Only Accept the Static members
* `BindingFlags.Public:` Only accept the public members
* `BindingFlags.NonPublic:` Only accept the nonpublic members

> **Code example:**
```csharp
// Here `|` needs to satisify all of these three Flags members will be get
fis = typeof(TargetClass).GetFields(BindingFlags.NonPublic | BindingFlags.Public | BindingFlags.Static);
```


---

# **System.Reflection.Assembly**

Assembly Class can get `Assembly's information`, dynamically `loads` the Assembly, and finds `type` information in the Assembly and `creates Instance` of that type.

---

## **Reflection through the name of the Assembly**

```csharp
Assembly ass = Assembly.Load("ClassLibrary");
Type t = ass.GetType("ClassLibrary.NewClass");
object o = Activator.CreateInstance(t);
MethodInfo mi = t.GetMethod("show");
mi.Invoke(o, null);
```

---

## **Reflection through Dll file name**

```csharp
Assembly assembly = Assembly.LoadFrom("xxx.dll's path");
    Type[] aa = a.GetTypes();
    foreach(Type t in aa)
    {
        if(t.FullName == "a.b.c")
        {
            object o = Activator.CreateInstance(t);
        }
    }
```

---

# **Purpose and Usage of Reflection**

---

## **Create Instance Dynamicly**

When we are creating the instance in C# we normally will create the object statically.

> For example, we have three classes, ClassA, ClassB, ClassC, each of them has a function.

```csharp
public class ClassA
{
    public void FunctionA()
    {
        Debug.Log("Run Function A");
    }
}
public class ClassB
{
    public void FunctionB()
    {
        Debug.Log("Run Function B");
    }
}
public class ClassC
{
    public void FunctionC()
    {
        Debug.Log("Run Function C");
    }
}
```

> When we want to create certain object through code, we have to use switch case or if statement to make decision and create instance one by one, which is staticly create instance.

```csharp
public class ReflectionExample : MonoBehaviour
{

    private void Start()
    {
        CreateObject("A");
        CreateObject("B");
        CreateObject("C");
    }


    public static void CreateObject(string className)
    {
        switch (className)
        {
            case "A":
                ClassA a = new ClassA();
                a.FunctionA();
                break;
            case "B":
                ClassB b = new ClassB();
                b.FunctionB();
                break;
            case "C":
                ClassC c= new ClassC();
                c.FunctionC();
                break;
            default:
                break;
        }
    }
}
```

> However, We can use `Reflection` to create Object `Dynamicly` based on the type's name:

```csharp
namespace ReflectionDemo
{
    public class ClassA
    {
        public void FunctionA()
        {
            Debug.Log("Run Function A");
        }
    }
    public class ClassB
    {
        public void FunctionB()
        {
            Debug.Log("Run Function B");
        }
    }
    public class ClassC
    {
        public void FunctionC()
        {
            Debug.Log("Run Function C");
        }
    }

    public class ReflectionExample01 : MonoBehaviour
    {

        private void Start()
        {
            DynamicCreateObject("A");
            DynamicCreateObject("B");
            DynamicCreateObject("C");
        }

        public static void DynamicCreateObject(string className)
        {
            var fullPathName = "ReflectionDemo.Class" + className;
            Type type = Type.GetType(fullPathName);

            var obj = Activator.CreateInstance(type);

            var method = obj.GetType().GetMethod("Function" + className);
            if (method != null)
            {
                method.Invoke(obj, null);
            }
        }
    }

}
```

---

>**End --Cheng Gu**

