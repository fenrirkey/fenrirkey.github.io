# Community Shaders Base Package
This is the base package that loads all the custom shaders.

## DLL
This is the main dll in the repo that enables the replacement of vanilla shaders with new shaders. 

Can be found primarily in https://github.com/doodlum/skyrim-community-shaders/tree/main/src

## Shaders
The default shaders will be found in https://github.com/doodlum/skyrim-community-shaders/tree/main/package/Shaders.

These default shaders should be overwritten by feature specific shaders.

# Features
These are custom features that are loaded after the default CS install.
## DLL
To add a new feature, the DLL needs to be modified. See prior examples of adding new features for the full list. https://github.com/doodlum/skyrim-community-shaders/pull/83
* feature.cpp https://github.com/doodlum/skyrim-community-shaders/blob/dev/src/Feature.cpp

The main features should live here: https://github.com/doodlum/skyrim-community-shaders/tree/dev/src/Features

### Virtual functions

#### Required functions:
- GetName
  - Used in ImGui feature list and json entry per feature
- GetShortName
  - Used for ini loading (No spaces usually)
- SetupResources
  - Called once in startup
- Reset
  - Called once per frame
- DrawSettings
  - Used for rendering imgui
- Draw
  - Normal rendering code here
- Save
  - Serialize settings to json
- RestoreDefaultSettings
  - Reset feature settings to default value

#### Functions that should be defined
- Load
  - Deserialize settings from json, also need to call `Feature::Load` after loading

#### Functions that can be overridden
These are already default defined in Feature.h but can be overriden if you need to do something extra like defined below
- GetShaderDefineName
  - used as an additional macro added when compiling shaders specified by HasShaderDefine, if the feature is loaded
- HasShaderDefine
  - if a shader type returns true, then GetShaderDefineName macro is added to its compilation, if the feature is loaded
- DrawDeferred
  - This one is not called yet, that is in the subsurface-scattering branch
- DataLoaded
  - Called by SKSE `kDataLoaded` event
- PostPostLoad
  - Called by SKSE `kPostPostLoad` event
- ClearShaderCache
  - Called by imgui clear shader cache button

## Shaders
Shaders are stored in https://github.com/doodlum/skyrim-community-shaders/tree/dev/features

# Register Usage
[(`dev` branch, 13th Apr, 2024)](https://github.com/doodlum/skyrim-community-shaders/wiki/Buffers)


# Debugging
To debug CS you will need to be able to debug both the cpp dll and the hlsl shader files.

## Remove DRM
1. Save a copy of the original exe so you can replace it. 
2. Use [Steamless](https://github.com/atom0s/Steamless) to strip the SteamDRM from the Skyrim.exe. This is required for a debugger to attach.
> Make sure to check `Keep Bind Section` in steamless, game will not boot without it.

## Disable ASLR
To ensure addresses don't move, disable ASLR. This can be done with [CFF Explorer](https://ntcore.com/?page_id=388). `Optional Header` -> `DLL Characteristics` -> `DLL can move`. Disable this.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/9010899e-57c3-4393-b4d5-d41f1baaed47)

## Attach

### Renderdoc
[Renderdoc](https://renderdoc.org/) can be used to debug shaders. 

1. Disable incompatible features: 
   1. [Skyrim Upscaler](https://www.nexusmods.com/skyrimspecialedition/mods/80343) 
   2. reshade 
   3. Frame Generation - Advanced -> Disable At Boot
2. **Optional** Enable [Global Hooking](https://renderdoc.org/docs/window/capture_attach.html#global-process-hook). Make sure to `Enable Global Hook` which will grey out all settings. This also will prevent closing renderdoc until you disable the setting.
3. Set up `Launch Application` so it will find the skse.exe (or Skyrim.exe if global hooking) when it launches. 

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/601cf0ba-c6df-44af-b676-0c6619836668)

4. Launch Skyrim. You will know RenderDoc has connected because of the message in the top left of Skyrim `Capturing D3D11`. If it doesn't show, try toggling the global hook and launching again.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/1c0bad05-7adc-478b-bf09-ac78a8153ca5)

5. Enable Developer Mode. Developer Mode can be enabled by setting the `Advanced Settings` -> `Log Level` to `TRACE` or `DEBUG`.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/afc3c2c4-9bce-4dfb-8f24-e683369cd971)

6. Enable `Advanced` -> `Extended Frame Annotations` to help populate info in the rendering process.

![image](https://github.com/user-attachments/assets/43293e36-31e0-4cbd-a027-be51412f9452)

6. Clear Shader Cache and Disk Cache. This is necessary to save debug information in the shaders to access named buffers/hlsl in renderdoc.

7. In game, press `F12` to capture the scene.
8. In Renderdoc, `File` -> `Attach to Running Instance`. Select Skyrim and `Connect to App`.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/b0566fbe-bcbb-4b58-8184-13cfaf5a2c4a)

9. Once attached, a new tab will appear. Double click any captures to load. 

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/9a056a6b-0201-4e07-afa4-073f5bbf9e7f)

10. You can verify you are seeing debug information by opening up the Pipeline tab and checking the Vertex or Pixel shaders. The resources should be named. In this case, we're getting `water.hlsl` data.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/5d47ce68-eec2-43d1-989e-f62acc09e866)

# Debugging Individual Shaders

It is possible to block individual shaders in game to find a faulty shader.

1. Find the faulty mesh in game. Confirm the mesh is caused by CS by toggling CS using the `Toggle Effects Key` (default `Numpad *`). The faulty mesh should disappear. If it doesn't, it's a Vanilla bug. This is an example with CS disabled:

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/f0e3b88e-8f37-4fb3-a4d0-edcaa33c3d42)

CS Enabled showing faulty mesh (note texture is missing in red circle):

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/e1da72c7-c28d-4bfa-bb4e-b108580d5e6b)

2. Enable Developer Mode. Developer Mode can be enabled by setting the `Advanced Settings` -> `Log Level` to `TRACE` or `DEBUG`.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/afc3c2c4-9bce-4dfb-8f24-e683369cd971)

3. Hit the `PageUp` or `PageDown` to cycle all active shaders until the faulty mesh disappears. This should match the vanilla shader.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/9ef81aa6-26e6-484d-ad3a-296b24df71d2)

4. [Optional] Stop blocking shaders by clicking on the `Advanced Settings` -> `Stop Blocking Shaders` button. This should also flush the log and also provide a noticeable log entry to identify the last shader blocked. This button only appears when shaders are being blocked.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/f70dccc0-8643-43ce-8115-7030ed522be9)

5. Review the CommunityShaders.log file. The blocked shader should be the last entry before `Stopped blocking shaders` (from step 4). In the example below, the ID/descriptor is `12000004` and it is a Lighting/Vertex shader, with the compile options of `WETNESS_EFFECTS LIGHT_LIMIT_FIX COMPLEX_PARALLAX_MATERIALS DYNAMIC_CUBEMAPS LODLANDNOISE LODLANDSCAPE MODELSPACENORMALS SHADOWSPLITCOUNT=3`. With this information, we can look at `lighting.hlsl` to figure out what hlsl is active.

```log
[2023-11-20 19:09:59.451] [debug] [33112] [ShaderCache.cpp:1557] Blocking shader (6/93) Lighting:Vertex:WETNESS_EFFECTS LIGHT_LIMIT_FIX COMPLEX_PARALLAX_MATERIALS DYNAMIC_CUBEMAPS LODLANDNOISE LODLANDSCAPE MODELSPACENORMALS SHADOWSPLITCOUNT=3 
[2023-11-20 19:09:59.451] [debug] [33112] [ShaderCache.cpp:1209] Skipping blocked shader 12000004:Lighting:Vertex:WETNESS_EFFECTS LIGHT_LIMIT_FIX COMPLEX_PARALLAX_MATERIALS DYNAMIC_CUBEMAPS LODLANDNOISE LODLANDSCAPE MODELSPACENORMALS SHADOWSPLITCOUNT=3  total: 1
[2023-11-20 19:10:10.734] [debug] [33112] [ShaderCache.cpp:1568] Stopped blocking shaders
``` 

For comparison, if we block the corresponding Pixel shader, the coloring will be distorted instead which is revealed to be ID/descriptor `12000005` which is the Lighting:Pixel shader.

![image](https://github.com/doodlum/skyrim-community-shaders/assets/7086117/f40cc236-8a91-4400-8e77-cd463922461e)
```
[2023-11-20 23:07:17.546] [debug] [70860] [ShaderCache.cpp:1537] Blocking shader (24/97) Lighting:Pixel:WETNESS_EFFECTS LIGHT_LIMIT_FIX COMPLEX_PARALLAX_MATERIALS DYNAMIC_CUBEMAPS LODLANDNOISE LODLANDSCAPE MODELSPACENORMALS SHADOWSPLITCOUNT=3 VC 
[2023-11-20 23:07:17.564] [debug] [74308] [ShaderCache.cpp:1243] Skipping blocked shader 12000005:Lighting:Pixel:WETNESS_EFFECTS LIGHT_LIMIT_FIX COMPLEX_PARALLAX_MATERIALS DYNAMIC_CUBEMAPS LODLANDNOISE LODLANDSCAPE MODELSPACENORMALS SHADOWSPLITCOUNT=3 VC  total: 1
```

# Resources
* [HLSL Tutorial](http://rbwhitaker.wikidot.com/hlsl-tutorials)
* [HLSL Development Cookbook](https://github.com/jjuiddong/HLSL-Development-Cookbook/tree/master)
* [HLSL Coordinates- World/View/Projection](https://www.codinglabs.net/article_world_view_projection_matrix.aspx)