---
layout: post
title:  "Matrix TRS"
date:   2018-05-17 14:15:05 +0000
image: /assets/images/post3.png
---

Normally when we develop programs, unavoidably want to do all sorts of transformation processing to the image. Sometimes the transformation can be more complicated, like the translation and then the rotation, scaling.

It is not only complicated but also inefficient. We can combine multiple transformations by using transformation matrix and matrix multiplication. Finally, we can get the desired result by using a matrix for each point.

{: .center}
![screenshots](/assets/images/PostImages/matrix_rts.png){:height="80%" width="80%"}

---
#### 1. Translate (T)

![dot](/assets/images/PostImages/t.png){:height="30%" width="30%"}

eg:Translate (3, -2, 1.5) from a start point (2, 3, 5)
{% highlight csharp %}
    public Matrix4x4 matrix;
    public Vector4 vector;
    void Start()
    {
        // if the w is 1 represent a point, 0 represent a vector
        vector = new Vector4(transform.position.x, transform.position.y, transform.position.z, 1);
        matrix = Matrix4x4.identity;
        Debug.Log("Before Translate: " + transform.localToWorldMatrix);
        //Before Translate: 
        //    1.00000	0.00000	0.00000	2.00000
        //    0.00000	1.00000	0.00000	3.00000
        //    0.00000	0.00000	1.00000	5.00000
        //    0.00000	0.00000	0.00000	1.00000
        
        
        matrix.m03 = 3; // move (3,-2,1.5)
        matrix.m13 = -2;
        matrix.m23 = 1.5f;
        
        
        // Translate
        vector = matrix * vector;
        transform.position = new Vector3(vector.x, vector.y, vector.z);
        Debug.Log("After Translate: " + transform.localToWorldMatrix);
        //After Translate: 
        //    1.00000	0.00000	0.00000	5.00000
        //    0.00000	1.00000	0.00000	1.00000
        //    0.00000	0.00000	1.00000	6.50000
        //    0.00000	0.00000	0.00000	1.00000
    }
{% endhighlight %}

---
#### 2. Rotate (R)
(1) Rotate Along X-Axis:
In 3D, when P(x,y,z) rotate along the x-axis and got P'(x',y',z'). x will not change. the Y and Z composite a plane with O (the origin) - ***YOZ*** so:
   x′=x 
   y′=ycosθ−zsinθ 
   z′=ysinθ+zcosθ 
   
Normally use Matrix4X4 represent this transformation: 

![dot](/assets/images/PostImages/r_x.png){:height="30%" width="30%"}

(2) Rotate Along Z-Axis: 

![dot](/assets/images/PostImages/r_z.png){:height="30%" width="30%"}


(3) Rotate Along Y-Axis:
when P(x,y,z) rotate along the y-axis got P'(x',y',z'). y will not change. the ZOX composite a plane***(NOT XOZ)*** so:

![dot](/assets/images/PostImages/r_y.png){:height="30%" width="30%"}

---
Above Described the roation in matrix. These three are all similar except rotating along the y-axis. The reason is the y-axis have a different axial order. 
1. XYZ ---Along x-axis
2. {: style="color: red"}YZX ---Along y-axis (NOT YXZ, Order changed)
3. ZXY ---Along z-axis

So, when rotating along y-axis,

it should transpose from **[x y z 1]** =>
![dot](/assets/images/PostImages/y_r_format.png){:height="10%" width="10%"}

eg: rotate along respectively x, y, z 30 degree:
{% highlight csharp %}
    Matrix4x4 matrix;
    Vector4 vector;
    public float angle = 30;

    public enum Axis { X, Y, Z }
    public Axis axis;

    void Start()
    {
        matrix = Matrix4x4.identity;
        Debug.Log("Before Rotate: " + matrix);
        //Before Rotate: 1.00000  0.00000 0.00000 0.00000
        //               0.00000 1.00000 0.00000 0.00000
        //               0.00000 0.00000 1.00000 0.00000
        //               0.00000 0.00000 0.00000 1.00000
        switch (axis)
        {
            case Axis.X:
                matrix.m11 = Mathf.Cos(angle * Mathf.Deg2Rad);
                matrix.m12 = -Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m21 = Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m22 = Mathf.Cos(angle * Mathf.Deg2Rad);
                Debug.Log("After Rotate(along x-axis) : " + matrix);

                //After Rotate(along x-axis):   1.00000   0.00000   0.00000   0.00000
                //                              0.00000   0.86603  -0.50000   0.00000
                //                              0.00000   0.50000   0.86603   0.00000
                //                              0.00000   0.00000   0.00000   1.00000

                break;
            case Axis.Y:
                matrix.m01 = Mathf.Cos(angle * Mathf.Deg2Rad);
                matrix.m02 = Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m21 = -Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m22 = Mathf.Cos(angle * Mathf.Deg2Rad);
                Debug.Log("After Rotate(along y-axis): " + matrix);
                //After Rotate(along y-axis):   1.00000   0.86603   0.50000   0.00000
                //                              0.00000   1.00000   0.00000   0.00000
                //                              0.00000   -0.50000  0.86603   0.00000
                //                              0.00000   0.00000   0.00000   1.00000
                break;
            case Axis.Z:
                matrix.m01 = Mathf.Cos(angle * Mathf.Deg2Rad);
                matrix.m02 = -Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m11 = Mathf.Sin(angle * Mathf.Deg2Rad);
                matrix.m12 = Mathf.Cos(angle * Mathf.Deg2Rad);
                Debug.Log("After Rotate(along z-axis): " + matrix);

                //After Rotate(along z-axis):   1.00000   0.86603  -0.50000   0.00000
                //                              0.00000   0.50000   0.86603   0.00000
                //                              0.00000   0.00000   1.00000   0.00000
                //                              0.00000   0.00000   0.00000   1.00000
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
{% endhighlight %}

---
#### 3. Scale (S)

![dot](/assets/images/PostImages/s.png){:height="30%" width="30%"}

eg: scale (1.5, 2, 3) from the atart scale (1, 1, 1)

{% highlight csharp %}
 public Matrix4x4 matrix;
    public Vector4 vector;

    void Start()
    {
        Debug.Log("Before scale: " + transform.localToWorldMatrix);
        //Before scale: 1.00000   0.00000 0.00000 2.00000
        //              0.00000 1.00000 0.00000 3.00000
        //              0.00000 0.00000 1.00000 5.00000
        //              0.00000 0.00000 0.00000 1.00000


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

        //After scale: 1.50000    0.00000 0.00000 2.00000
        //             0.00000 2.00000 0.00000 3.00000
        //             0.00000 0.00000 3.00000 5.00000
        //             0.00000 0.00000 0.00000 1.00000
    }
{% endhighlight %}

---

>**End --Cheng Gu**

