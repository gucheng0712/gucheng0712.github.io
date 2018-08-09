---
layout: post
title:  "Texture Mapping"
date:   2018-06-20 14:15:05 +0000
image: /assets/images/post7.png
---

The basic idea of “texture mapping” is to put a flat image onto the surface of a three-dimensional shape.

---
#### 1. Create a Single Texture Shader

{: .center}
![dot](/assets/images/PostImages/st.png){:height="70%" width="70%"}

{% highlight cg %} 
    Shader "MyShader/SingleTexture"
{
    Properties
    {
        _MainTex("Texture",2D) ="white"{}
    }
    SubShader
    {
        Pass
        {
            Tags{"LightMode" = "ForwardBase"}
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            // In order to use some built-in variables, 
            // we need to use "Lighting.cginc" Library which is builtin unity
            #include "Lighting.cginc"

            sampler2D _MainTex;

            // Compared with other properties, we not only need to declare a sampler2D type variable,
            // but also we should declare a float4 type variable _MainTex_ST. 
            // Names are not random. We MUST follow "Texture Name"_ST. 
            // S represents scale, T represents transformation.
            float4 _MainTex_ST;

            struct a2v
            {
                float4 vertex : POSITION;
                float4 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 pos : SV_POSITION;
                float2 uv : TEXCOORD0;
            };

            v2f vert(a2v v)
            {
                v2f f;
                f.pos = mul(UNITY_MATRIX_MVP, v.vertex);


                f.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
                // or just call below function which builtin cg
                //f.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
                return f;
            }

            fixed4 frag(v2f f) : SV_Target
            {
                fixed3 c = tex2D(_MainTex, f.uv).rgb;
                return fixed4(c,1); 
            } 
            ENDCG
        }
    }
    FallBack "Diffuse"
}
{% endhighlight %}

---
#### 2. Combine Texture with Lighting Model

{: .center}
![dot](/assets/images/PostImages/texinLightmodel.png){:height="70%" width="70%"}

{% highlight cg %} 
Shader "MyShader/TextureInLightModel"
{
    Properties
    {
        _Color("Color", Color) = (1,1,1,1)
        _MainTex("Texture", 2D) ="white"{}
        _Specular("Specular", Color) = (1,1,1,1)
        _Gloss("Gloss", Range(5,100)) = 5
    } 

    SubShader
    {
        Pass
        {
            Tags{"LightMode" = "ForwardBase"}
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "Lighting.cginc"

            sampler2D _MainTex;
            float4 _MainTex_ST;

            fixed4 _Color;
            fixed4 _Specular;
            float _Gloss;

            struct a2v
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float4 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 pos : SV_POSITION;
                float4 worldVertex : TEXCOORD0;
                float3 worldNormal : TEXCOORD1;
                float2 uv : TEXCOORD2;
            };

            v2f vert(a2v v)
            {
                v2f f;
                f.pos = UnityObjectToClipPos(v.vertex);
                f.worldNormal = mul(_Object2World, v.normal);
                f.worldVertex = mul(_Object2World, v.vertex);
                f.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;
                // or just call below function which builtin cg
                //f.uv = TRANSFORM_TEX(v.texcoord, _MainTex);
                return f;
            }

            fixed4 frag(v2f f) : SV_Target
            {
                fixed3 worldNormalDir = normalize(f.worldNormal);
                fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(f.worldVertex));
                fixed3 albedo = tex2D(_MainTex, f.uv).rgb * _Color.rgb;
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz * albedo;
                fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(worldNormalDir, worldLightDir));

                fixed3 viewDir = normalize(UnityWorldSpaceViewDir(f.worldVertex));
                fixed3 halfDir = normalize(viewDir + worldLightDir);
                fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(halfDir,worldNormalDir)),_Gloss);

                fixed3 c = ambient + diffuse + specular;

                return fixed4(c,1); 
            } 
            ENDCG
        }
    }
    FallBack "Diffuse"
}

{% endhighlight %}

---

>**End --Cheng Gu**

