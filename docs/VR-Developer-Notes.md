# VR HLSL porting
VR porting assumes that the flat shader has been reversed already and exists in CS for flat. We typically do not RE or port for VR first due to the added complexity.

1. Use `Advanced -> Dump Shaders` to dump all shaders for Flat/VR on startup. It will end up in `data\ShaderDump`.
2. Use [3dmigitos decompiler](https://github.com/bo3b/3Dmigoto/tree/master/HLSLDecompiler) on all the .bin shaders to get hlsl. We have a script `Skyrim_Disassemble_Shaders` that we haven't posted yet that will crawl all of the bin files. (ask on discord)
3. Check cbuffers vs flat. Same size should be fine. Search this regex (VSCode escaped) in VR will identify eyeIndex based buffers. `cb[0-9]+\[([^\]]+\+)+\w+\]`. Compare against flat files for same cb (non regex). For example, 

```cpp
//FLAT
  r1.xyzw = float4(0,0,0,1) * cb12[8].wwww;
//VR
  r2.xyzw = cb12[r0.z+16].wwww * float4(0,0,0,1);
```
These are essentially the same buffer access of cb12. The addition order doesn't matter, and the additional index of `r0.z` is basically `eyeIndex`. The offset of `+ 16` shows that the variable at `packoffset(c8)` in flat is actually `packoffset(c16)` in VR. This is due to the fact we're in a set of arrays that use eyeIndex. Specifically, this matches what we've determined here: https://github.com/doodlum/skyrim-community-shaders/blob/dev/package/Shaders/Common/FrameBuffer.hlsl#L6.

Update the CS flat hlsl to use the right VR cbuffers. Use the define `VR` for VR specific code.

4. On identifying eyeIndex based cbuffers, edit the CS flat hlsl that you are porting to now use the eyeIndex.
5. Fix stereoUV coords. VR renders both left and right eye at the same time vs flat which is a single eye. So certain coords in flat UV space may need to be converted to stereoUV. `Depthtexture` is a prime example. See other [examples](https://github.com/search?q=repo%3Adoodlum%2Fskyrim-community-shaders%20ConvertToStereoUV&type=code). Keep track of the coordinate system acccessing the texture. For example UV or texcoord (in compute shaders) will need to be stereo.

Helpful VR functions can be found in https://github.com/doodlum/skyrim-community-shaders/blob/dev/package/Shaders/Common/VR.hlsli