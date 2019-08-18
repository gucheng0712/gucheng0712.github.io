---
layout: post
title:  "The Magic of Stencil Shader"
date:   2018-07-15 
categories: [Cg, Unity]
tags: [Unity, Shader, Math,CG]
icon: icon-splatter
---

**Stencil Test** is the test between **Alpha Test** and **Depth Test** in the Rendering Pipeline. The purpose is to set up the size of **reference value** and the stencil buffer's value based on the condition. If the condition is not satisifed, then discard this fragment/pixel.

> Pseudocode：

``` csharp
if(referenceValue & readMask comparisonFunction stencilBuffValue & readMask)
    pass the fragment to the next test
else
    discar the fragment
```

---
#### Stencil Test Syntax in Unity Shaderlab

``` csharp
stencil
{
	Ref referenceValue
	ReadMask  readMaskValue
	WriteMask writeMaskValue
	Comp comparisonFunction
	Pass stencilOperation
	Fail stencilOperation
	ZFail stencilOperation
}
```

* **Ref**: Ref is used to set the *reference value(Range between 0~255)*. 

* **ReadMask**: ReadMask's readMaskValue performs bitwise and(&) operations with the **referenceValue** and **stencilBufferValue**. readMaskValue is also an integer between 0~255, (By Default the value is 255, the binary bit is 11111111, which means, it will not effect the **:refernceValue and stencilBufferValue**) 

* **WriteMask**: same as **ReadMask**, but the writeMaskValue **only** performs bitwise and(&) operations with **stencilBufferValue**.

* **Comp**: the comparisonFunction is the condition, the keywords includes: 
    * **Greater**: Only display pixels with reference values > buffer values.
    * **GEqual**: Only display pixels with reference values >= buffer values.
    * **Less**: Only display pixels with reference values < buffer values.
    * **LEqual**: Only display pixels with reference values <= buffer values.
    * **Equal**: Only display pixels with reference values = buffer values.
    * **NotEqual**: Only display pixels with reference values != buffer values.
    * **Always**: Always display pixels.
    * **Never**: Never display pixels.
    
* **Pass**: Processing stencil operation after **pass** the condition. (**default**: Keep)
* **Fail**: Processing stencil operation after **fail** the condition. (**default**: Keep)
* **ZFail**: Processing stencil operation after **fail the ztest(depth test)** the condition.(**default**: Keep)
    * stencilOperation's **type** and **definition**:
        *  **Keep**: keep the current buffer's content, which means stencilBufferValue will not change.
        * **Zero**: stencilBufferValue = 0
        * **Replace**: stencilBufferValue = referenceValue
        * **IncrSat**: stencilBufferValue += 1, min(stencilBufferValue,255)
        * **DecrSat**: stencilBufferValue -= 1, max(stencilBufferValue,0)
        * **Invert**: stencilBufferValue = ~stencilBufferValue
        * **IncrWrap**: stencilBufferValue += 1, if stencilBufferValue>255, stencilBuffer=0
        * **DecrWrap**: stencilBufferValue -= 1, if stencilBufferValue<0, stencilBuffer=255

> **With all that said, the main points are as follows:**
1. **stencilBufferValue** and **referenceValue** are the two most important value in stencil test.
2. **Stencil Test** is all about the comparison between **stencilBufferValue** and **referenceValue**. Never, Always, Less, LEqual, Greater, Equal, NotEqual, Always, and Never.
3. After Stencil Test, processing stencil operation to the stencilBufferValue, includes Keep, Zero, Replace, IncrSat, DecrSat, Invert, IncrWrap, and DecrWrap.

---
# **An Simple Example to Achieve Magic Gate by Using Stencil Shader**

This example needs **two shader**, one is a mask for the window(the frame is normal shader with out stencil), and another mask is for the character:

<p align="center">     
<img src="/static/assets/img/blog/stencilmask1.gif" width="40%">
</p>

> 1. **In the window mask shader, the core is:**

```csharp
    ColorMask 0
    ZWrite Off
    Stencil
    {
        Ref 1
        Comp Always
        Pass Replace
    }
```

In this code, **ColorMask 0** is used for masking the output of the color, which means don't output any color onto screen. **ZWrite Off** is to make sure the character's pixels will not be discarded during the Depth Test (Ztest). Set **Ref** as 1, and **Comp Always** to make sure the stencil test always pass, **Pass Replace** will update the stencilBufferValue to referenceValue, which is 1. The main function of this shader code is to update the stencil buffer value to 1 without output any color.

> 2. **In the character mask shader, the core is:**

```csharp
    Stencil
    {
        Ref 1
        Comp Equal
    }
```

In this code, **Ref 1** is to set referenceValue as 1, then **Comp Equal**, which means if referenceValue=stencilBufferValue, then stencil test pass, otherwise discard. In the scene the area that stencilBufferValue = 1 is the window mask area, so only this area can pass the stencil test, which means only within the windows, character will be shown onto the screen.

> **Full Code for Window Mask Shader**:

```csharp
Shader "MyShader/WindowMask"
{
	Properties
	{
		_ID("Mask ID", Int) = 1
	}
	SubShader
	{
		// The Rendering queue need to make sure the window mask is smaller than character(geomety) mask
		Tags{ "RenderType" = "Opaque" "Queue" = "Geometry-1" }
		ColorMask 0 // discard color
		ZWrite off 

		Stencil
		{
			Ref[_ID]
			Comp Always
			Pass Replace
		}

		Pass
		{
			CGINCLUDE
			struct appdata
			{
				float4 vertex : POSITION;
			};
			struct v2f
			{
				float4 pos : SV_POSITION;
			};

			v2f vert(appdata v)
			{
				v2f o;
				o.pos = UnityObjectToClipPos(v.vertex);
				return o;
			}
			half4 frag(v2f i) : SV_Target
			{
				return half4(1,1,1,1);
			}
			ENDCG
		}
	}
}
```

> **Full Code for Character Mask Shader**:

```csharp
Shader "MyShader/CharacterMask"
{
	Properties
	{
		_MainTex("Base (RGB)", 2D) = "white" {}
        _ID("Mask ID", Int) = 1
	}
	
	SubShader
	{
		Tags { "Queue" = "Geometry" "RenderType" = "Opaque" }

		Stencil
		{
			Ref [_ID]
			Comp Equal
		}

		Pass
		{
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "UnityCG.cginc"

			sampler2D _MainTex;
			float4 _MainTex_ST;

			struct appdata_t
			{
				float4 vertex : POSITION;
				float2 texcoord : TEXCOORD0;
			};
			struct v2f
			{
				float4 vertex : SV_POSITION;
				half2 texcoord : TEXCOORD0;
			};

			v2f vert(appdata_t v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);
				o.texcoord = TRANSFORM_TEX(v.texcoord, _MainTex);
				return o;
			}
			fixed4 frag(v2f i) : SV_Target
			{
				fixed4 col = tex2D(_MainTex, i.texcoord);
				UNITY_OPAQUE_ALPHA(col.a);
				return col;
			}
			ENDCG
		}
	}
}

```

---

>**End --Cheng Gu**

