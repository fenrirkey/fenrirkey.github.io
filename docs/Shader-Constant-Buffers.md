This is a temporary scratch pad.

# Image Shaders
## PS
Based on reviewing VanillaHDR code
```cpp
#pack(cbuffer)

struct cb2
{
	float scaleBloom; // treat at bool > 0.5 = true
	float3 Params01_0_yzw;
	float4 Params01_2;
	float bloomFactor;
	float whiteFactor;
	float filmicFactor; // treat at bool > 0.5 = true
	float Param01_2_w;
	float saturation;
	float Param01_3_y;
	float contrast;
	float brightness;
#ifdef FADE
	float3 fade;
	float fadeWeight;
#endif
	float3 tintColor;
	float tintWeight;
}
```
Initial review of VR shows that `tintColor` and `tintWeight` appear to be 0.
