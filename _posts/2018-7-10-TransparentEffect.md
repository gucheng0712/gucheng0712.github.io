---
layout: post
title:  "Transparent Effects"
date:   2018-07-10 14:15:05 +0000
image: /assets/images/post10.png
---

In Game Development, we often use two ways to achieve transparent effect, Aplha Test and Alpha Blending.



---
#### 1. Z-Buffer (Depth Buffer)

{: .center}
![dot](/assets/images/PostImages/z_buffer.png){:height="50%" width="50%"}

>Z-Buffer is used to solve the problems of visibility. It can decide which parts of the object to render first, and which parts of the objects will render later(can be blocked). 

The basic idea is to determine the distance between the camera and the object based on the value of the z-buffer. For example, when we want to render an fragment, the fragment's depth value will be compared to the depth in the z-buffer (If the ZTest is On). If its depth value is smaller, it means this object is more far away from the camer. so this frament should not be rendered onto screen (which means it is covered by something); Otherwise, this fragment should replace the depth value which is in the z-buffer. 

---
#### 2. Alpha Test vs Alpha Blending

{: .center}
![dot](/assets/images/PostImages/alpha.gif){:height="70%" width="70%"}

***Alpha Test:***: As long as there is one fragment's alpha value does not meet the conditions, the fragment which corresponding to it will be discarded (into a completely transparent fragment). Otherwise, it will be rendered as an opaque object.

***Alpha Blending***: Through the Alpha blending, we can get a translucent effect. it will combine the current fragment's alpha value with the color value which has already been stored in the color buffer, to form a new color, but Alpha Blending needs to Turn Off the ZWrite. Therefore, we need to be careful with the rendering order when we are doing alpha blending.  

---

#### 3. Create A Alpha Test Shader In Unity 

{: .center}
![dot](/assets/images/PostImages/alpha_test.png){:height="70%" width="70%"}

{% highlight cg %} 
Shader "MyShader/AlphaTest" 
{
    Properties
    {
        _Color("Color", Color) = (1,1,1,1)
        _MainTex("Main Texture", 2D)= "white"{}
        _Cutoff("Alpha Cutoff", Range(0,1)) = 0.5
    }
    SubShader
    {
        // Usually when utilize the alpha test we should have these three tags
        Tags { "Queue" = "AlphaTest" "IgnoreProjector" = "True" "RenderType" = "TransparentCutout" }
        Pass
        {
            Tags{ "LightMode" = "ForwardBase" }

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "Lighting.cginc"

            fixed4 _Color;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            fixed _Cutoff;

            struct a2v
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float4 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldVertex : TEXCOORD1;
                float2 uv : TEXCOORD2;
            };

            v2f vert(a2v v)
            {
                v2f f;
                f.pos = mul(UNITY_MATRIX_MVP, v.vertex);
                f.worldNormal = mul(_Object2World, v.normal);
                f.worldVertex = mul(v.vertex, _World2Object);
                f.uv = v.texcoord.xy*_MainTex_ST.xy+_MainTex_ST.zw;
                return f;
            }

            fixed4 frag(v2f f): SV_Target
            {
                fixed3 worldNormal = normalize(f.worldNormal);
                fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(f.worldVertex));
                fixed4 texColor = tex2D(_MainTex, f.uv);

                // Alpha Test
                if((texColor.a - _Cutoff) < 0.0){ discard; }
                // or use clip function
                //clip(texColor.a - _Cutoff);

                fixed3 albedo = texColor.rgb * _Color.rgb;
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
                fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(worldNormal,worldLightDir));
                fixed3 c = ambient + diffuse;
                return fixed4(c, 1.0);

            }

            ENDCG
        }
    }
}
{% endhighlight %}

---

#### 4. Create A Alpha Blending Shader In Unity 

{: .center}
![dot](/assets/images/PostImages/Alpha Blending.png){:height="70%" width="70%"}

{% highlight cg %} 
Shader "MyShader/AlphaBlending" 
{
    Properties
    {
        _Color("Main Tint", Color) =(1,1,1,1)
        _MainTex("Main Texture", 2D) = "white"{}
        _AlphaScale ("Alpha Scale", Range(0, 1)) = 1
    }

    SubShader
    {
        Tags{ "Queue" = "Transparent" "IgnoreProjector" = "True" "RenderType" = "Transparent" }


        // The first Pass only for store the model's depth info into the z-buffer,
        // to eliminate the fragment which covered by itself in the model
        Pass
        {
            ZWrite On 
            // ColorMask use for mask Color in the model.
            // ColorMask RGB: Mask RGB Color
            // ColorMask A: Mask Alpha Channel
            // ColorMask 0: Mask all color, but since ZWrite is On, it will only pass the z-buffer
            ColorMask 0 
        }

        Pass
        {
            Tags{ "LightMode" = "ForwardBase" }
            ZWrite Off // not passing  z-buffer

            // The semantic for blending mode
            // Blend Off : Close Blending

            // Blend SrcFactor DstFactor: Open Blending, and Set Blending factor.
            // Color in the Color Buffer = The object's fragment color * SrcFactor + The color which is already in color buffer * DstFactor

            // Blend SrcFactor DstFactor, SrcFactorA DstFactorA: Same as above but using different factor for blending alpha channel

            Blend SrcAlpha OneMinusSrcAlpha


            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #include "Lighting.cginc"

            fixed4 _Color;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            fixed _AlphaScale;

            struct a2v
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float4 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldVertex : TEXCOORD1;
                float2 uv : TEXCOORD2;
            };

            v2f vert(a2v v)
            {
                v2f f;
                f.pos = UnityObjectToClipPos(v.vertex);
                f.worldNormal = mul(unity_ObjectToWorld, v.normal);
                f.worldVertex = mul(v.vertex, unity_WorldToObject);
                f.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
                return f;
            }

            fixed4 frag(v2f f): SV_Target
            {
                fixed3 worldNormal = normalize(f.worldNormal);
                fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(f.worldVertex));
                fixed4 texColor = tex2D(_MainTex,f.uv);
                fixed3 albedo = texColor.rgb * _Color.rgb;
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
                fixed3 diffuse = _LightColor0.rgb * albedo *saturate(dot(worldNormal,worldLightDir));
                fixed3 c = diffuse + ambient;
                return fixed4(c,texColor.a * _AlphaScale);
            }
            ENDCG
        }
    } 
}
{% endhighlight %}

---

#### 5. ShaderLab Blending Command

* * *

| Command | Description |
|Blend SrcFactor DstFactor|Open Blending, and Set Blending factor.Color in the Color Buffer = The object's fragment color * SrcFactor + The color which is already in color buffer * DstFactor|
|Blend SrcFactor DstFactor, SrcFactorA DstFactorA|Same function as above, butbut using different factor for blending alpha channe|

* * *

{: style="color: red"}
>So, What are these factors?

* * *

| Parameters | Description |
| One | Factor is 1 |
| Zero | Factor is 0 |
| SrcColor | Factor is the source color value |
| SrcAlpha | Factor is the source alpha value |
| DstColor | Factor is the target color value |
| DstAlpha | Factor is the target alpha value |
| OneMinusSrcColor | Factor is 1 - source color value |
| OneMinusSrcAlpha | Factor is 1 - source alpha value |
| OneMinusDstColor | Factor is 1 - target color value |
| OneMinusDstAlpha | Factor is 1 - target alpha value |

* * *

{: style="color: red"}
>We also can sue ShaderLab's BlendOp Command to perform various blending operations

* * *

| Operation | Description |
| Add | O = SrcFactor * SourceColor + DstFactor * TargetColor |
| Sub | O = SrcFactor * SourceColor - DstFactor * TargetColor |
| RevSub | O = DstFactor * TargetColor - SrcFactor * SourceColor |
| Min | O = (min(S.r, D.r), min(S.g, D.g), min(S.b, D.b), min(S.a, D.a)) |
| Max | O = (max(S.r, D.r), max(S.g, D.g), max(S.b, D.b), max(S.a, D.a)) |

* * *

{: style="color: red"}
>The Common Blending Type:

{: .center}
![dot](/assets/images/PostImages/Blending Mode.png){:height="70%" width="70%"}

{% highlight cg %}
Blend SrcAlpha OneMinusSrcAlpha  // Normal

Blend OneMinusDstColor One  // Soft Additive

Blend DstColor Zero  // Multiply

Blend DstColor SrcColor  // 2x Multiply

BlendOp Min    // Darken
Blend One One

BlendOp Max   // Lighten
Blend One One

Blend OneMinusDstColor One // Screen
Blend One OneMinusSrcColor // Same as above

Blend One One // Linear Dodge

{% endhighlight %}



---

>**End --Cheng Gu**
