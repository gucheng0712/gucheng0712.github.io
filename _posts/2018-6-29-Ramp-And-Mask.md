---
layout: post
title:  "Ramp & Mask Texture"
date:   2018-06-29 14:15:05 +0000
image: /assets/images/post9.png
---

Ramp Texture allows us to control the diffuse light of an object. Mask Texture can protect some part of the texture not be influence.

---
#### 1. Ramp Texture

{: .center}
![dot](/assets/images/PostImages/ramp.png){:height="70%" width="70%"}

{% highlight cg %} 
Shader "MyShader/RampMap"
{
    Properties {
        _Color ("Color Tint", Color) = (1, 1, 1, 1)
        _RampTex ("Ramp Tex", 2D) = "white" {}
        _Specular ("Specular", Color) = (1, 1, 1, 1)
        _Gloss ("Gloss", Range(8.0, 256)) = 20
    }
    SubShader {
        Pass { 
            Tags { "LightMode"="ForwardBase" }
        
            CGPROGRAM
            
            #pragma vertex vert
            #pragma fragment frag

            #include "Lighting.cginc"
            
            fixed4 _Color;
            sampler2D _RampTex;
            float4 _RampTex_ST;
            fixed4 _Specular;
            float _Gloss;
            
            struct a2v {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float4 texcoord : TEXCOORD0;
            };
            
            struct v2f {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldPos : TEXCOORD1;
                float2 uv : TEXCOORD2;
            };
            
            v2f vert(a2v v) {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                
                o.worldNormal = UnityObjectToWorldNormal(v.normal);
                
                o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                
                o.uv = TRANSFORM_TEX(v.texcoord, _RampTex);
                
                return o;
            }
            
            fixed4 frag(v2f i) : SV_Target {
                fixed3 worldNormal = normalize(i.worldNormal);
                fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
                
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
                
                // Use the texture to sample the diffuse color
                fixed halfLambert  = 0.5 * dot(worldNormal, worldLightDir) + 0.5;
                fixed3 diffuseColor = tex2D(_RampTex, fixed2(halfLambert, halfLambert)).rgb * _Color.rgb;
                
                fixed3 diffuse = _LightColor0.rgb * diffuseColor;
                
                fixed3 viewDir = normalize(UnityWorldSpaceViewDir(i.worldPos));
                fixed3 halfDir = normalize(worldLightDir + viewDir);
                fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(worldNormal, halfDir)), _Gloss);
                
                return fixed4(ambient + diffuse + specular, 1.0);
            }
            ENDCG
        }
    } 
}
{% endhighlight %}


---
#### 2. Mask Texture 

{: .center}
![dot](/assets/images/PostImages/mask.png){:height="70%" width="70%"}

{% highlight cg %} 
Shader "MyShader/7. MaskMap"
{
    Properties
    {
        _Color("Color", Color) = (1,1,1,1)
        _MainTex("MainTex",2D) = "white"{}
        _BumpMap("Normal Map",2D) = "bump"{}
        _BumpScale("Bump Scale", Float) = 1.0
        _SpecularMask ("Specular Mask", 2D) = "white"{}
        _SpecularScale("Specular Scale", Float) = 1
        _Specular("Specular", Color) =(1,1,1,1)
        _Gloss("Gloss",Range(5,100)) = 5
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

            fixed4 _Color;
            sampler2D _MainTex;
            float4 _MainTex_ST;
            sampler2D _BumpMap;
            float _BumpScale;
            sampler2D _SpecularMask;
            float _SpecularScale;
            fixed4 _Specular;
            float _Gloss;

            struct a2v
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
                float4 tangent : TANGENT;
                float4 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 pos : SV_POSITION;
                float2 uv : TEXCOORD0;
                float3 lightDir : TEXCOORD1;
                float3 viewDir : TEXCOORD2;
            };

            v2f vert(a2v v)
            {
                v2f f;
                f.pos = UnityObjectToClipPos(v.vertex);
                f.uv = v.texcoord.xy * _MainTex_ST.xy + _MainTex_ST.zw;

                TANGENT_SPACE_ROTATION;

                f.lightDir = mul(rotation, ObjSpaceLightDir(v.vertex)).xyz;
                f.viewDir = mul(rotation, ObjSpaceViewDir(v.vertex)).xyz;

                return f;
            }

            fixed4 frag(v2f f) : SV_Target
            {
                fixed3 tangentLightDir = normalize(f.lightDir);
                fixed3 tangentViewDir = normalize(f.viewDir);

                fixed3 tangentNormalDir = UnpackNormal(tex2D(_BumpMap,f.uv));
                tangentNormalDir.xy *= _BumpScale;
                tangentNormalDir.z = sqrt(1 - saturate(dot(tangentNormalDir.xy, tangentNormalDir.xy)));

                fixed3 albedo = tex2D(_MainTex, f.uv).rgb * _Color.rgb;
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.rgb * albedo;
                fixed3 diffuse = _LightColor0.rgb * albedo * saturate(dot(tangentLightDir,tangentNormalDir));

                fixed3 halfDir = normalize(tangentViewDir + tangentLightDir);

                fixed specularMask = tex2D(_SpecularMask, f.uv).r *_SpecularScale;
                fixed3 specular = _LightColor0.rgb * _Specular.rgb * specularMask * pow(saturate(dot(halfDir,tangentNormalDir)),_Gloss);

                fixed3 c = ambient + diffuse + specular;

                return fixed4 (c,1);
            }
            ENDCG
        }
    }
}

{% endhighlight %}

---

>**End --Cheng Gu**

