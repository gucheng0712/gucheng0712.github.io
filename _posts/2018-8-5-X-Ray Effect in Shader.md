---
layout: post
title:  "X-Ray Effect in Shader"
date:   2018-08-5 14:15:05 +0000
image: /assets/images/post14.png
---

In many games, we can use special ability to see through the wall. So In this post I am going to make a X-ray effect.

A X-Ray is easy to achieve. basicly, just need to add an additional `Pass{}` in shader which the ***ZTest*** should set to ***Greater***, and the ZWrite should close. 

>This is the result of the experiment:

{: .center}
![dot](/assets/images/PostImages/X-Ray.gif){:height="50%" width="50%"}

---

>Code:

{% highlight cg %} 
Shader "MyShader/Transparent2"
{
	Properties
	{
        _MainTex("MainTex",2D) = ""{}
        _Color("Color", Color) = (1,1,1,1)
        _OccPower("Occlusion Power", Range(0,2)) = 0.5 
        _OccColor("Occlusion Color", Color) = (1,1,1,1)
	}
	SubShader
	{
        Tags{"RenderTyoe" = "Transparent" "Queue" = "Transparent"}
        
		Pass //  This pass is for X-ray
		{
            Blend DstAlpha One 

            // When the Z value of the object's pixel is greater 
            //than the Z value of the current camera on the pixel, it will pass Ztest
            ZTest Greater

            ZWrite Off
			CGPROGRAM
			#pragma vertex vert
            #pragma fragment frag
			
			#include "UnityCG.cginc"

            float _OccPower;
            fixed4 _OccColor;

			struct appdata
			{
				float4 vertex : POSITION;
                float3 normal : NORMAL;

			};

			struct v2f
			{
                float3 worldNormal : TEXCOORD0;
                float4 pos : SV_POSITION;
                float4 worldVertex : TEXCOORD1;
			};
            


			v2f vert (appdata v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
                o.worldNormal = normalize(mul(v.normal,unity_WorldToObject));
                o.worldVertex = normalize(mul(unity_ObjectToWorld, v.vertex));
                
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{

                float3 viewDir = normalize(_WorldSpaceCameraPos.xyz - i.worldVertex.xyz);

                // rim effect
                float rim = 1-saturate(dot(i.worldNormal,viewDir));

				fixed3 col = pow(rim, _OccPower) * _OccColor.rgb;

				return fixed4(col,0.5f);
			}
			ENDCG
		}



        Pass // This pass is for the model itself
        {
            Blend SrcAlpha OneMinusSrcAlpha
            ZWrite On
            ZTest LEqual 
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            
            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;
            fixed4 _Color;
            
            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }
            
            fixed4 frag (v2f i) : SV_Target
            {
                // sample the texture
                fixed3 col = tex2D(_MainTex, i.uv).rgb * _Color.rgb;

                return fixed4(col,1);
            }
            ENDCG
        }
	}
}

{% endhighlight %}


---

>**End --Cheng Gu**

