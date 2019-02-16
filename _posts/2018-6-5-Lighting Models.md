---
layout: post
title:  "Common Lighting Models"
date:   2018-06-05 
categories: [Cg, Unity]
tags: [Unity, Shader, Math,CG]
icon: icon-splatter
---
In this post, I focus on reviewing the Lighting Models I learned these days which are ***Diffuse(Lambert)***, ***Half-Lambert***, ***Specular***, and ***Blinn-Phong***. 

---
#### 1. Diffuse(Lambert)

<img src="/static/assets/img/blog/lambert.png" width="50%">
<img src="/static/assets/img/blog/Lambert rule.png" width="40%">
>
* M_diff：material color.
* S_diff：light source color.
* n · l：the radius of the angle between normal and light direction by using dot product to calculate.

(1) ***Lambert Per-Vertex-Level (Low Cost, but less accurate)***
```
Shader "MyShader/Diffuse Vertex-Level" 
{
    Properties 
    {
        _Diffuse ("Diffuse", Color) = (1, 1, 1, 1)
    }
    SubShader 
    {
        Pass 
        { 
            Tags { "LightMode"="ForwardBase" }
            
            CGPROGRAM
            
            #pragma vertex vert
            #pragma fragment frag
            
            #include "Lighting.cginc"
            
            fixed4 _Diffuse;
            
            struct a2v 
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
            };
            
            struct v2f 
            {
                float4 pos : SV_POSITION;
                fixed3 color : COLOR;
            };
            
            v2f vert(a2v v) 
            {
                v2f o;
                // Transform the vertex from object space to projection space
                o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
                
                // Get ambient term
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
                
                // Transform the normal from object space to world space
                fixed3 worldNormal = normalize(mul(v.normal, (float3x3)_World2Object));
                // Get the light direction in world space
                fixed3 worldLight = normalize(_WorldSpaceLightPos0.xyz);
                // Compute diffuse term
                fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLight));
                
                o.color = ambient + diffuse;
                
                return o;
            }
            
            fixed4 frag(v2f i) : SV_Target
            {
                return fixed4(i.color, 1.0);
            }
            ENDCG
        }
    }
    FallBack "Diffuse"
}
```

(2) ***Lambert Per-Pixel-Level (higher Cost, but more accurate)***
```
Shader "MyShader/Diffuse Pixel-Level" {
    Properties 
    {
        _Diffuse ("Diffuse", Color) = (1, 1, 1, 1)
    }
    SubShader 
    {
        Pass 
        { 
            Tags { "LightMode"="ForwardBase" }
            CGPROGRAM
            
            #pragma vertex vert
            #pragma fragment frag
            
            #include "Lighting.cginc"
            
            fixed4 _Diffuse;
            
            struct a2v 
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
            };
            
            struct v2f
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
            };
            
            v2f vert(a2v v) 
            {
                v2f o;
                // Transform the vertex from object space to projection space
                o.pos = mul(UNITY_MATRIX_MVP, v.vertex);

                // Transform the normal from object space to world space
                o.worldNormal = mul(v.normal, (float3x3)_World2Object);

                return o;
            }
            
            fixed4 frag(v2f i) : SV_Target 
            {
                // Get ambient term
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
                
                // Get the normal in world space
                fixed3 worldNormal = normalize(i.worldNormal);
                // Get the light direction in world space
                fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
                
                // Compute diffuse term
                fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir)); 
                fixed3 color = ambient + diffuse;
                
                return fixed4(color, 1.0);
            } 
            ENDCG
        }
    } 
    FallBack "Diffuse"
}
```

(3) ***Lambert in Unity Surface Shader***

```
Shader "MyShader/SurfaceShader_Diffuse" 
{
    Properties 
    {
        _Color ("Color", Color) = (1,1,1,1)
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
    }
    SubShader 
    {
        Tags { "RenderType"="Opaque" }

        CGPROGRAM
        #pragma surface surf SimpleLambert
        
        sampler2D _MainTex;
        fixed4 _Color;

        struct Input
        {
            float2 uv_MainTex;
        };
        
        // Custom Lighting Model
        half4 LightingSimpleLambert(SurfaceOutput s, half3 lightDir, half atten)
        {
            // Get the dot product of normal and light
            half NdotL = dot(s.Normal, lightDir);
        
            half3 color = s.Albedo * _LightColor0.rgb * saturate(NdotL) * atten);
            
            return fixed4(color,1);
        }

        void surf (Input IN, inout SurfaceOutput o) 
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
            o.Albedo = c.rgb;
        }
        ENDCG
    }
    FallBack "Diffuse"
}

```

---
#### 2. Half Lambert

<img src="/static/assets/img/blog/half_lambert.png" width="70%">

(2) ***HalfLambert in Unity vertex/fragment Shader***

```
Shader "MyShader/Half Lambert-Pixel-Level" 
{
    Properties 
    {
        _Diffuse ("Diffuse", Color) = (1, 1, 1, 1)
    }
    SubShader 
    {
        Pass 
        { 
            Tags { "LightMode"="ForwardBase" }
            
            CGPROGRAM
            
            #pragma vertex vert
            #pragma fragment frag
            
            #include "Lighting.cginc"
            
            fixed4 _Diffuse;
            
            struct a2v 
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
            };
            
            struct v2f 
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
            };
            
            v2f vert(a2v v) 
            {
                v2f o;
                // Transform the vertex from object space to projection space
                o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
                
                // Transform the normal from object space to world space
                o.worldNormal = mul(v.normal, (float3x3)_World2Object);
                
                return o;
            }
            
            fixed4 frag(v2f i) : SV_Target 
            {
                // Get ambient term
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
                
                // Get the normal in world space
                fixed3 worldNormal = normalize(i.worldNormal);
                // Get the light direction in world space
                fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
                
                // Compute diffuse term
                fixed halfLambert = dot(worldNormal, worldLightDir) * 0.5 + 0.5;
                fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * halfLambert;
                
                fixed3 color = ambient + diffuse;
                
                return fixed4(color, 1.0);
            }
            ENDCG
        }
    } 
    FallBack "Diffuse"
}
```

(2) ***HalfLambert in Unity Surface Shader***

```
Shader "MyShader/SurfaceShader_HalfLambert" 
{
    Properties 
    {
        _Color ("Color", Color) = (1,1,1,1)
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        CGPROGRAM
        #pragma surface surf HalfLambert
        
        sampler2D _MainTex;
        fixed4 _Color;

        struct Input
        {
            float2 uv_MainTex;
        };
        
        // Custom Lighting Model
        half4 LightingHalfLambert(SurfaceOutput s, half3 lightDir)
        {
            // Get the dot product of normal and light
            half NdotL = dot(s.Normal, lightDir);
            
            half halfLambert = NdotL * 0.5+0.5;
            
            half3 color = s.Albedo * _LightColor0.rgb * halfLambert;
            
            return fixed4(color,1);
        }
        void surf (Input IN, inout SurfaceOutput o) 
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
            o.Albedo = c.rgb;
        }
        ENDCG
    }
    FallBack "Diffuse"
}

```


---
#### 3. Specular(Phong)
<img src="/static/assets/img/blog/Specular.png" width="50%">
<img src="/static/assets/img/blog/Specular_rule.png" width="40%">

>
* M_spec：material color.
* S_spec：light source color.
* v · r: the radius of the angle between view direction and the reflection direction of the light direction and normal.


In Specular lighting model, shouldn't use per-vertex-level because it is not accurate with less vertex area.

(1) ***Phong in vertex&fragment Shader***

```
Shader "MyShader/Specular" 
{
    Properties 
    {
        _Diffuse ("Diffuse", Color) = (1, 1, 1, 1)
        _Specular ("Specular", Color) = (1, 1, 1, 1)
        _Gloss ("Gloss", Range(8.0, 256)) = 20
    }
    SubShader 
    {
        Pass 
        { 
            Tags { "LightMode"="ForwardBase" }
        
            CGPROGRAM
            
            #pragma vertex vert
            #pragma fragment frag

            #include "Lighting.cginc"
            
            fixed4 _Diffuse;
            fixed4 _Specular;
            float _Gloss;
            
            struct a2v 
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
            };
            
            struct v2f 
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldPos : TEXCOORD1;
            };
            
            v2f vert(a2v v) 
            {
                v2f o;
                // Transform the vertex from object space to projection space
                o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
                
                // Transform the normal from object space to world space
                o.worldNormal = mul(v.normal, (float3x3)_World2Object);
                // Transform the vertex from object spacet to world space
                o.worldPos = mul(_Object2World, v.vertex).xyz;
                
                return o;
            }
            
            fixed4 frag(v2f i) : SV_Target 
            {
                // Get ambient term
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
                
                fixed3 worldNormal = normalize(i.worldNormal);
                fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
                
                // Compute diffuse term
                fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * saturate(dot(worldNormal, worldLightDir));
                
                // Get the reflect direction in world space
                fixed3 reflectDir = normalize(reflect(-worldLightDir, worldNormal));
                // Reflection can be calculate as below:
                //      float NdotL = dot(worldNormal,worldLightDir);
                //      float reflectDir = normalize(2.0 * worldNormal * NdotL - worldLightDir);
                
                // Get the view direction in world space
                fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
                // Compute specular term
                fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(saturate(dot(reflectDir, viewDir)), _Gloss);
                
                return fixed4(ambient + diffuse + specular, 1.0);
            }
            ENDCG
        }
    } 
    FallBack "Specular"
} 
```

(2) ***Phong in Unity Surface Shader***

```
Shader "MyShader/SurfaceShader_Phong" 
{
    Properties 
    {
        _Color ("Color", Color) = (1,1,1,1)
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
        _SpecularColor("Specular Color",Color) = (1,1,1,1)
        _SpecPower("Specular Power",Range(0,30)) =1
    }
    SubShader 
    {
        Tags { "RenderType"="Opaque" }

        CGPROGRAM
        #pragma surface surf Phong
        
        sampler2D _MainTex;
        fixed4 _Color;
        fixed4 _SpecularColor;
        float _SpecPower;

        struct Input
        {
            float2 uv_MainTex;
        };
        
        // Custom Lighting Model
        fixed4 LightingPhong(SurfaceOutput s, fixed3 lightDir,fixed3 viewDir, fixed atten)
        {
            // Diffuse  
            half NdotL = dot(s.Normal, lightDir);
            float3 diffuse = max(0,NdotL) * atten;
            
            // Reflection
            float3 reflectDir = normalize(2.0*s.Normal*NdotL-lightDir);
            
            //Specular
            float spec = pow(max(0,dot(reflectDir,viewDir)),_SpecPower);
            float3 finalSpec = _SpecularColor.rgb*spec;
            
            // Final Effect
            half3 color = s.Albedo * _LightColor0.rgb * diffuse +(_LightColor0*finalSpec);
            
            return fixed4(color,1);
        }
        void surf (Input IN, inout SurfaceOutput o) 
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
            o.Albedo = c.rgb;
        }
        ENDCG
    }
    FallBack "Diffuse"
}
```

---
#### 4. Blinn-Phong

<img src="/static/assets/img/blog/blinn-phong.png" width="50%">
<img src="/static/assets/img/blog/blinn-rule.png" width="40%">

>
* h(halfway):  h is the middle vector between view direction and light direction (just like the axis of symmetry).

(1) ***Blinn-Phong in vertex&fragment Shader***

```
Shader "MyShader/Blinn-Phong"
{
    Properties 
    {
        _Diffuse ("Diffuse", Color) = (1, 1, 1, 1)
        _Specular ("Specular", Color) = (1, 1, 1, 1)
        _Gloss ("Gloss", Range(8.0, 256)) = 20
    }
    SubShader 
    {
        Pass 
        { 
            Tags { "LightMode"="ForwardBase" }
            CGPROGRAM
            
            #pragma vertex vert
            #pragma fragment frag
            
            #include "Lighting.cginc"
            
            fixed4 _Diffuse;
            fixed4 _Specular;
            float _Gloss;
            
            struct a2v 
            {
                float4 vertex : POSITION;
                float3 normal : NORMAL;
            };
            
            struct v2f 
            {
                float4 pos : SV_POSITION;
                float3 worldNormal : TEXCOORD0;
                float3 worldPos : TEXCOORD1;
            };
            
            v2f vert(a2v v) 
            {
                v2f o;
                // Transform the vertex from object space to projection space
                o.pos = mul(UNITY_MATRIX_MVP, v.vertex);
                
                // Transform the normal from object space to world space
                o.worldNormal = mul(v.normal, (float3x3)_World2Object);
                
                // Transform the vertex from object spacet to world space
                o.worldPos = mul(_Object2World, v.vertex).xyz;
                
                return o;
            }
            
            fixed4 frag(v2f i) : SV_Target 
            {
                // Get ambient term
                fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
                
                fixed3 worldNormal = normalize(i.worldNormal);
                fixed3 worldLightDir = normalize(_WorldSpaceLightPos0.xyz);
                
                // Compute diffuse term
                fixed3 diffuse = _LightColor0.rgb * _Diffuse.rgb * max(0, dot(worldNormal, worldLightDir));
                
                // Get the view direction in world space
                fixed3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldPos.xyz);
                // Get the half direction in world space
                fixed3 halfDir = normalize(worldLightDir + viewDir);
                // Compute specular term
                fixed3 specular = _LightColor0.rgb * _Specular.rgb * pow(max(0, dot(worldNormal, halfDir)), _Gloss);
                
                return fixed4(ambient + diffuse + specular, 1.0);
            }  
            ENDCG
        }
    } 
    FallBack "Specular"
}
```

(2) ***Blinn-Phong in Unity Surface Shader***

```

Shader "MyShader/SurfaceShader_BilnnPhong" 
{
    Properties 
    {
        _Color ("Color", Color) = (1,1,1,1)
        _MainTex ("Albedo (RGB)", 2D) = "white" {}
        _SpecularColor("Specular Color",Color) = (1,1,1,1)
        _SpecPower("Specular Power",Range(8,255)) =1
	}
    SubShader 
    {
        Tags { "RenderType"="Opaque" }

        CGPROGRAM
        #pragma surface surf CustomBlinnPhong
        
        sampler2D _MainTex;
        fixed4 _Color;
        fixed4 _SpecularColor;
        float _SpecPower;

        struct Input
        {
            float2 uv_MainTex;
        };
        
        // Custom Lighting Model
        half4 LightingCustomBlinnPhong(SurfaceOutput s, fixed3 lightDir, half3 viewDir, half atten)
        {
            // Get the dot product of normal and light
            half NdotL = dot(s.Normal, lightDir);
            
            half3 diffuse = _LightColor0.rgb * _Color.rgb * saturate(NdotL);
           
            // Get the direction between lighrDir and viewDir
            half3 halfDir = normalize(lightDir + viewDir);
            half NdotH = dot(s.Normal, halfDir);
            
            half3 specular = _LightColor0.rgb * _SpecularColor.rgb * pow(max(0,NdotH), _SpecPower);
            half3 color =s.Albedo * diffuse +specular;
            
            return half4(color,1);
        }
        void surf (Input IN, inout SurfaceOutput o) 
        {
            fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
            o.Albedo = c.rgb;
        }
        ENDCG
    }
	FallBack "Diffuse"
}

```

---

>**End --Cheng Gu**

