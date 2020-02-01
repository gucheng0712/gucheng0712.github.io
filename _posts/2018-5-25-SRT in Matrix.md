---
layout: post
title:  "Matrix SRT"
date:   2018-05-25 
categories: [Math, Unity, Csharp]
tags: [Unity,Csharp,Math,CG]
icon: fa-calculator
---

Normally when we develop programs, unavoidably want to do all sorts of transformation processing to the image. Especically the transformation bewtween multiple coordinate system.

It is not only complicated but also inefficient. We can combine multiple transformations by using transformation matrix and matrix multiplication. Finally, we can get the desired result by using a matrix for each point.

{: style="color: red"}
>Important Note: The order of Transformation does affect the result. In most cases, The order of Matrix Transformation is ***Scale -> Rotate -> Translate***. 

<p align="center">   
<img src="/static/assets/img/blog/srt.png" width="80%">
</p>

---
#### 1. Translate (T)

<p align="center">  
<img src="/static/assets/img/blog/t.png" width="40%">
</p>

A change from one location to another is represented by a translation matrix, T. This matrix translates an entity by a vector $t = (tx, ty, tz)$. $T$ is given below by

<p align="center">  
<img src="/static/assets/img/blog/translate.png" width="40%">
</p>
<p align="center">  
Note: The square on the left is transformed with a translation matrix T(5, 2, 0), whereby the square is moved 5 distance units to the right and 2 upward.
</p>

<br>
<br>

> Code Example: Translate (3, -2, 1.5) from a start point (2, 3, 5)

```csharp
    public Matrix4x4 matrix;
    public Vector4 vector;
    void Start()
    {
        // if the w is 1 represent a point, 0 represent a vector
        vector = new Vector4(transform.position.x, transform.position.y, transform.position.z, 1);
        matrix = Matrix4x4.identity;
        Debug.Log("Before Translate: " + transform.localToWorldMatrix);

        matrix.m03 = 3; // move (3,-2,1.5)
        matrix.m13 = -2;
        matrix.m23 = 1.5f;
        
        // Translate
        vector = matrix * vector;
        transform.position = new Vector3(vector.x, vector.y, vector.z);
        Debug.Log("After Translate: " + transform.localToWorldMatrix);
    }
```

>Output:

```
Before Translate: 
    1.00000    0.00000    0.00000    2.00000
    0.00000    1.00000    0.00000    3.00000
    0.00000    0.00000    1.00000    5.00000
    0.00000    0.00000    0.00000    1.00000

After Translate: 
    1.00000    0.00000    0.00000    5.00000
    0.00000    1.00000    0.00000    1.00000
    0.00000    0.00000    1.00000    6.50000
    0.00000    0.00000    0.00000    1.00000
```

---
#### 2. Rotate (R)


<p align="center">  
<img src="/static/assets/img/blog/matrixRot.png" width="25%">
</p>

In two dimensions, the rotation matrix is simple to derive. Assume that we have
a vector, $v = (v_x, v_y)$, which we parameterize as $v = (v_x, v_y) = (r cos \theta, r sin \theta)$. If we were to rotate that vector by $\phi$ radians (counterclockwise), then we would get $u = (r cos(\theta + \phi), r sin(\theta + \phi))$. This can be rewritten as

\begin{equation}
    u = \begin{bmatrix} 
        rcos(\theta+\phi) \\\
        rsin(\theta+\phi)
    \end{bmatrix} = 
    \begin{bmatrix} 
        r(cos\theta cos\phi - sin\theta sin\phi) \\\
        r(sin\theta cos\phi + cos\theta sin\phi) 
    \end{bmatrix} = 
    \begin{bmatrix}
        cos\phi & -sin\phi \\\
        sin\phi & cos\phi
    \end{bmatrix} 
    \begin{bmatrix} 
        r cos\theta \\\
        r sin\theta
    \end{bmatrix} = 
    R(\phi)v
\end{equation}

(1) Rotate Along X-Axis:

<img src="/static/assets/img/blog/r_x.png" width="30%">

(2) Rotate Along Z-Axis: 

<img src="/static/assets/img/blog/r_z.png" width="30%">

(3) Rotate Along Y-Axis:
when P(x,y,z) rotate along the y-axis got P'(x',y',z'). y will not change. the ZOX composite a plane***(NOT XOZ)*** so:

<img src="/static/assets/img/blog/r_y.png" width="30%">

---
Above Described the roation in matrix. These three are all similar except rotating along the y-axis. The reason is the y-axis have a different axial order. 
1. XYZ ---Along x-axis
2. {: style="color: red"}YZX ---Along y-axis (NOT YXZ, Order changed)
3. ZXY ---Along z-axis

So, when rotating along y-axis,

it should transpose from $
    \begin{bmatrix} 
        x & y & z & 1
    \end{bmatrix} =>
      \begin{bmatrix} 
        x \\\ 
        y \\\ 
        z \\\
         1
    \end{bmatrix}
$


>Code Example: Rotate along respectively x, y, z 30 degree

```csharp
    Matrix4x4 matrix;
    Vector4 vector;
    public float angle = 30;

    public enum Axis { X, Y, Z }
    public Axis axis;

    void Start()
    {
        matrix = Matrix4x4.identity;
        Debug.Log("Before Rotate: " + matrix);

        switch (axis)
        {
            case Axis.X:
                matrix.m11 = Mathf.Cos(angle * Mathf.Deg2Rad);
                matrix.m12 = -Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m21 = Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m22 = Mathf.Cos(angle * Mathf.Deg2Rad);
                Debug.Log("After Rotate(along x-axis) : " + matrix);
                break;
            case Axis.Y:
                matrix.m01 = Mathf.Cos(angle * Mathf.Deg2Rad);
                matrix.m02 = Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m21 = -Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m22 = Mathf.Cos(angle * Mathf.Deg2Rad);
                Debug.Log("After Rotate(along y-axis): " + matrix);
                break;
            case Axis.Z:
                matrix.m01 = Mathf.Cos(angle * Mathf.Deg2Rad);
                matrix.m02 = -Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m11 = Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m12 = Mathf.Cos(angle * Mathf.Deg2Rad);
                Debug.Log("After Rotate(along z-axis): " + matrix);
                break;
        }

        transform.rotation = MatrixToQuaternion(matrix);
    }

    Quaternion MatrixToQuaternion(Matrix4x4 _matrix)
    {
        float qw = Mathf.Sqrt(1f + _matrix.m00 + _matrix.m11 + _matrix.m22) / 2;
        float w = 4 * qw;
        float qx = (_matrix.m21 - _matrix.m12) / w;
        float qy = (_matrix.m02 - _matrix.m20) / w;
        float qz = (_matrix.m10 - _matrix.m01) / w;

        return new Quaternion(qx, qy, qz, qw);
    }
```

>Output:

```
Before Rotate: 
        1.00000  0.00000 0.00000 0.00000
        0.00000 1.00000 0.00000 0.00000
        0.00000 0.00000 1.00000 0.00000
        0.00000 0.00000 0.00000 1.00000

After Rotate(along x-axis): 
        1.00000   0.00000   0.00000   0.00000
        0.00000   0.86603  -0.50000   0.00000
        0.00000   0.50000   0.86603   0.00000
        0.00000   0.00000   0.00000   1.00000
                
After Rotate(along y-axis): 
        1.00000   0.86603   0.50000   0.00000
        0.00000   1.00000   0.00000   0.00000
        0.00000   -0.50000  0.86603   0.00000
        0.00000   0.00000   0.00000   1.00000
                            

After Rotate(along z-axis): 
        1.00000   0.86603  -0.50000   0.00000
        0.00000   0.50000   0.86603   0.00000
        0.00000   0.00000   1.00000   0.00000
        0.00000   0.00000   0.00000   1.00000
```

---
#### 3. Scale (S)

<img src="/static/assets/img/blog/s.png" width="30%">

eg: scale (1.5, 2, 3) from the atart scale (1, 1, 1)

```csharp
 public Matrix4x4 matrix;
    public Vector4 vector;

    void Start()
    {
        Debug.Log("Before scale: " + transform.localToWorldMatrix);

        matrix = Matrix4x4.identity;

        // if the w is 1 represent a point, 0 represent a vector
        vector = new Vector4(transform.localScale.x, transform.localScale.y, transform.localScale.z, 1);

        //scale times
        matrix.m00 = 1.5f; // scale x
        matrix.m11 = 2; // scale y
        matrix.m22 = 3; // scale z

        vector = matrix * vector;
        transform.localScale = new Vector3(vector.x, vector.y, vector.z);
        Debug.Log("After scale: " + transform.localToWorldMatrix);
    }
```

>Output:

```
Before scale: 
        1.00000   0.00000 0.00000 2.00000
        0.00000 1.00000 0.00000 3.00000
        0.00000 0.00000 1.00000 5.00000
        0.00000 0.00000 0.00000 1.00000

After scale: 
        1.50000    0.00000 0.00000 2.00000
        0.00000 2.00000 0.00000 3.00000
        0.00000 0.00000 3.00000 5.00000
        0.00000 0.00000 0.00000 1.00000
```

---

>**End --Cheng Gu**

