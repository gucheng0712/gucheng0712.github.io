---
layout: post
title:  "Dynamic Discoloration Shader"
date:   2018-07-29 14:15:05 +0000
image: /assets/images/post12.png
---
An exercise of writing surface shader with vertex function to create a color lerp shader

---

{: .center}
![dot](/assets/images/PostImages/Model Dynamic Discoloration.gif){:height="70%" width="70%"}

{% highlight cg %} 
Shader "MyShader/CarPaint_SurfaceShader" 
{
	Properties 
    {
        _MainColor("MainColor", Color) = (1,1,1,1)
        _SecondColor("SecondColor", Color) = (1,1,1,1)
        _Center("Center", Range(-0.7,0.7)) = 0
        _Radius("R", Range(0,0.5)) = 0.2
        _Glossiness ("Smoothness", Range(0,1)) = 0.5
        _Metallic ("Metallic", Range(0,1)) = 0.0
        _MainTex("Main Texture", 2D) = ""{}
    }
	SubShader {
        Tags { "RenderType"="Opaque" }
        
        CGPROGRAM

        // Physically based Standard lighting model, and enable shadows on all light types
        #pragma surface surf Standard fullforwardshadows vertex:vert
        

        float4 _MainColor;
        float4 _SecondColor;
        float _Center;
        float _Radius;
        half _Glossiness;
        half _Metallic;
        sampler2D _MainTex;
        
        struct Input 
        {
            float2 uv_MainTex;
            float z;
        };

        void vert(inout appdata_full v, out Input o)
        {
            // UNITY_INITIALIZE_OUTPUT(Input,o);
            // or
            o.uv_MainTex = v.texcoord.xy;
            o.z = v.vertex.z;
        }
        
        void surf (Input IN, inout SurfaceOutputStandard o) 
        {
            // Albedo comes from a texture tinted by color
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex);
            o.Albedo = c.rgb;
                
            // Metallic and smoothness come from slider variables
            o.Metallic = _Metallic;
            o.Smoothness = _Glossiness;
            o.Alpha = c.a;

            
            
            float d = IN.z - _Center;
            float dLength = abs(d);
            
            // Get 1 / -1 to determine the color
            // the vertices > _Center, it will be 1
            // the vertices < _Center, it will be -1
            d = d / dLength;
            
            // the pure level represents the _MainColor and _SecondColor value
            // when the pureLevel increase, the blending between two color will increase
            float pureLevel = dLength /_Radius;
            pureLevel = saturate(pureLevel);
            
            // This determine the negative or positive value
            d *= pureLevel;

            d = d * 0.5+0.5;
               
            o.Albedo *= lerp(_MainColor, _SecondColor, d);
        }
        ENDCG
    }
    FallBack "Diffuse"
}
{% endhighlight %}

---

>**End --Cheng Gu**

