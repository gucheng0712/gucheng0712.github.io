---
layout: post
title:  "Dynamic Discoloration Shader"
date:   2018-07-29 
categories: [Cg, Unity]
tags: [Unity, Shader, Math,CG]
icon: icon-splatter
---
An exercise of writing surface shader with vertex function to achieve dynamic discoloration of the model and cube map reflection.

---

<p align="center">     
<img src="/static/assets/img/blog/Model Dynamic Discoloration.gif" width="70%">
</p>

```
Shader "MyShader/CarPaint_SurfaceShader" 
{
	Properties 
    {
        _MainColor("MainColor",Color) = (1,1,1,1)
        _SecondColor("SecondColor",Color) = (1,1,1,1)
        _Center("Center",Range(-0.7,0.7)) = 0
        _Radius("R", Range(0,0.5)) = 0.2
		_Glossiness ("Smoothness", Range(0,1)) = 0.5
		_Metallic ("Metallic", Range(0,1)) = 0.0
        _MainTex("Main Texture",2D) = ""{}
        _CubeMap("Cube Map",Cube)=""{}
    }
	SubShader {
		Tags { "RenderType"="Opaque" }

		CGPROGRAM

		// Physically based Standard lighting model, and enable shadows on all light types
		#pragma surface surf Standard fullforwardshadows vertex:vert
        
        #pragma target 3.5 // needs it for dx 11

        float4 _MainColor;
        float4 _SecondColor;
        float _Center;
        float _Radius;
        half _Glossiness;
        half _Metallic;
		sampler2D _MainTex;
        samplerCUBE _CubeMap;

		struct Input 
        {
			float2 uv_MainTex;
            float3 R;
            float z;
		};

        void vert(inout appdata_full v, out Input o)
        {
            // UNITY_INITIALIZE_OUTPUT(Input,o);
            // or
            o.uv_MainTex = v.texcoord.xy;

            float3 V = -WorldSpaceViewDir(v.vertex);
            // float3 V = mul(_Object2World,v.vertex).xyz - _WorldSpaceCameraPos.xyz;
            // or
            float3 N = normalize(mul(v.normal,(float3x3)unity_WorldToObject));

            o.R = reflect(V,N);
            o.z = v.vertex.z;// determine the direction the lerp
        }
        

		void surf (Input IN, inout SurfaceOutputStandard o) {
			// Albedo comes from a texture tinted by color
			fixed4 c = tex2D (_MainTex, IN.uv_MainTex);
            fixed4 cubeMapCol = texCUBE(_CubeMap,IN.R);

            // Combine texture color and the cubemap reflection color
			o.Albedo = c.rgb + cubeMapCol.rgb;

			// Metallic and smoothness come from slider variables
			o.Metallic = _Metallic;
			o.Smoothness = _Glossiness;
			o.Alpha = c.a;


            float d = IN.z - _Center;
            float dLength = abs(d);
            
            // Get 1 or -1 to determine the color on two sides
            // the vertices > _Center, it will be 1
            // the vertices < _Center, it will be -1
            d = d / dLength;

            // The pure level represents the _MainColor and _SecondColor value
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
```

---

>**End --Cheng Gu**

