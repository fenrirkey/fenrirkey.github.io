# FAQ

# Installation & compatibility
<details>
  <summary>

Where can I find the latest CS version?</summary>
  <p>The latest stable version is always on <a href='https://www.nexusmods.com/skyrimspecialedition/mods/86492'>Nexus</a>. If you want to play stable, use this.<br>
If you are interested to try out the latest development versions, those are usually pinned in one channels on <a href='https://discord.gg/nkrQybAsyy'>CS discord</a>. The different channels reflect different development branches. Some are more experimental, some already further down the development road. Do some reading to find out which one is the one you would like to try.
</p>
</details>

<details>
  <summary>

How to install Community Shaders?</summary>
  <p>

1. Uninstall alternative shaders mods, e.g. remove enbseries folder and .ini files<br>
1. Read the instructions on the <a href='https://www.nexusmods.com/skyrimspecialedition/mods/86492'>mod page</a><br>
1. Install all necessary <b>Requirements</b> mentioned on the mod page<br>
1. Install Community Shaders as any other mod<br>
1. Check <b>Features</b> section on the mod page and install all those you would like to use (generally all). Yes, this is quite a number.<br>
1. Check the <b>Compatibility</b> section on the mod page. Disable the mods that are not compatible in case you use them.
</p>
</details>

<details>
  <summary>

In which order do I have to place CS mods?</summary>
  <p>Does not matter for most part. Only 'Community Shaders' should be first of the pack so that 'Grass Lighting' and 'Tree LOD Lighting' overwrite it.<br>
</p>
</details>

<details>
  <summary>

Do I still need ENB Light, Cubemap Reflections or Capture Warmer with CS?</summary>
  <p>As of the latest CS 0.7.5 beta, ENB Light is not needed anymore as LLF covers lights for those meshes.<br>
Also Cubemap Reflections and Capture Warmer are not required anymore.
</p>
</details>

<details>
  <summary>

Do I still need Lux split meshes / can I optimise draw calls here?</summary>
  <p>Lux splits bigger meshes into smaller pieces to workaround the limit of allowing only 4 light sources per mesh/object. With smaller objects, chances are better that not too many light sources shed light on an object. However this  increases draw calls, because much more objects must be rendered. CS does not have this limit so it is better to install Lux without the mesh options in fomod.
</p>
<p> Answer from author of Lux (03/11/2024): These meshes main purpose is to help with engine limitations indeed so using CS light limit fix makes them optional.
I'm fixing a lot of vanilla meshes when optimizing them though so you won't have these fixes anymore if you're not installing them. I'm also reworking most mods texture sets because the new meshes have different trishape structures, thus keep in mind that you'll need to delete my own texture set edits on vanilla meshes or they'll be all over the place if not using my meshes.
Split meshes increase drawcalls so it's up to you. I mean if you can survive that you can just keep them. If the performance issue is a priority then you should remove them.

Be careful with injected meshes though, I'm replacing models here and there, so any mesh with non vanilla name should be kept unless you fix that directly in the patch plugin. They're often named Lux_xxx so they can't be missed. If I remember well I'm using these meshes in Blackreach (mountain stuff) and some floor I needed to replace in a player house (Skyfall Estate).</p>

</details>

<details>
  <summary>

Does CS work with the Skyrim Upscaler and Reshade?</summary>
  <p>Yes, but only the <a href='https://www.nexusmods.com/skyrimspecialedition/mods/80343'>Nexus version</a> of the Upscaler is supported. Other versions may or may not work.
</p>
</details>


# Configuration
<details>
  <summary>

How can I open CS menu to change/activate/deactivate some of the effects?</summary>
  <p>Press end key on your keyboard. <br>
If playing in VR, virtual keyboard does not work and the menu is not shown in VR mode. Change to desktop mode and press the key on your physical keyboard.
</p>
</details>

<details>
  <summary>

Can I also edit settings other than the CS menu?</summary>
  <p>Yes, the first time you change any setting CS generates a settings file CommunityShadersUSER.json in your overwrite folder under SKSE\Plugins. You can edit this file. The default settings are stored in Community Shaders mod under SKSE\Plugins\CommunityShaders.json. It is not the suggested way to edit settings there, but technically possible.
</p>
</details>

<details>
  <summary>

Do I need to change ini for PP as I did for Vanilla HDR?</summary>
  <p> No, these are not used anymore. (fLightingOutputColourClampPostSpec, fLightingOutputColourClampPostEnv, fLightingOutputColourClampPostLit)
</p>
</details>

# Development of new features
<details>
  <summary>

Will new feature X be supported on VR / AE version?</summary>
  <p>New features are usually explored and fleshed out for one version (mostly 1.5.97) in the initial phase. If the team is happy with it and it can be ported features are often enabled for other versions later. But there are no guarantees/commitments.<br>
Be patient and do not try to push
</p>
</details>

<details>
  <summary>

I have a new idea and would like to have a special effect shader developed!</summary>
  <p>Use this motivation to <a href='https://github.com/doodlum/skyrim-community-shaders/wiki/Developers'>read up on CS development</a> and start your first steps developing your first shader. Please spend some time reading an experimenting on your on to show your engagement and feel free to join the discord to discuss then.<br>
You are also also free to mention your idea within <a href='https://discord.com/channels/1080142797870485606/1173059013261414440'>feature-request section</a> on discord, but please do not engage into discussions with CS developers to analyse for you. They have a big backlog and even more own ideas. Also please do not excert pressure via donations as this is not intended for contract work, but to support overall CS development.
</p>
</details>


# Errors
<details>
  <summary>

There are strange/broken effect with CS!</summary>
  <p>

1. Check that the version you are using is supported. Test versions generally support 1.5.97, other versions if explicitly stated<br>
1. See <a href='https://github.com/doodlum/skyrim-community-shaders/wiki/FAQ#communityshadersjson-is-created-blank--starting-the-game-results-in-a-freeze'>here</a> to validate that configuration files are generated correctly<br>
1. Delete Shadercache and disk cache as described <a href='https://github.com/doodlum/skyrim-community-shaders/wiki/FAQ#communityshadersjson-is-created-blank--starting-the-game-results-in-a-freeze'>here</a> to regenerate<br>
1. Make sure that all parts of Community Shaders are of the same version. Read <a href='https://www.nexusmods.com/skyrimspecialedition/mods/86492'>Community Shaders Nexus page</a> to see which mods are part of Community Shaders. You cannot mix parts of different CS versions<br>
1. Check that the error is really coming from CS. Disable CS and check if the issue is gone.<br>
1. Make sure that the issue is reproducible and note down how it can be reproduced.<br>
1. Check that Shaders are loaded without issues when starting Skyrim. Otherwise, please save the CS log file.<br>
1. If your game crashes, make sure that you use <a href='https://www.nexusmods.com/skyrimspecialedition/mods/59818'>Crash Logger</a>. Do not use Trainwreck. Save your crashlog. 
Report the issue including the logs and steps to reproduce via <a href='https://discord.com/channels/1080142797870485606/1082470370902294600'>User Help</a> channel on discord
</p>
</details>

<details>
  <summary>

CommunityShaders.json is created blank / starting the game results in a freeze</summary>
<p>

1. Delete CommunityShaders.json and CommunityShadersUser.json. You can ususally find it in your Overwrite folder under SKSE/Plugins. See <a href='https://imgur.com/a/MOGhDmH'>here</a> how to access your Overwrite folder in MO2. Start the game again and check that CommunityShaders.json is created with some content (not blank / not 0kb size).<br>
1. If this does not help, and you are using <a href='https://www.nexusmods.com/skyrimspecialedition/mods/18860'>PrivateProfileRedirector</a> make sure that it is updated to the latest version. Version 0.5.x or lower is known to cause this issue. Please update to version 0.6.x or higher.
</p>
</details>

<details>
  <summary>

Shaders compilation leads to CTD</summary>
<p>
Shader compiler comes from Microsoft and cannot be fixed by CS team. There are such cases known on weaker GPU if you max out the GPU during compiling. You can try reducing the shader compiler threads ("Background Compiler Threads" & "Compiler Threads"). Search for a file 'CommunityShadersUSER.json' and edit those config values. If you have not yet successfully ran CS and do not have an own config file open 'Community Shaders' mod go to SKSE\Plugins and edit 'CommunityShaders.json'. This is not best practice and you have to change those values again after your next CS update, but it may bring you past the compilation.
</p>
</details>


<details>
  <summary>

Shaders failed to compile! Red colored errors!</summary>
<p>
You most likely combined incompatible versions of CS and its features, or the shader cache did not regenerate after update. Either use the newest Nexus releases or a single test build from Discord, never combine them. Delete CS, its features and shader cache (Overwrite folder in MO2) and install CS again without combining versions. If not fixed, follow the steps in the previous answer. 
</p>
</details>
<details>
  <summary>

How to fix if the eyes of some NPC look strange?
</summary>
  <p>You have two options to fix this:<br>
- Either hide eyecubemap.dds from 'Dynamic Cubemaps - Metal'<br>- Get a mod like <a href='https://www.nexusmods.com/skyrimspecialedition/mods/21833'>Subtle eye cubmap</a> and overwrite 'Dynamic Cubemaps - Metal'</p>
</details>

<details>
  <summary>

Terrain does not look parallaxed!
</summary>
  <p>

1. Make sure you're using terrain textures with parallax support, like <a href='https://www.nexusmods.com/skyrimspecialedition/mods/89542'>Atlantean Landscapes</a> and <a href='https://www.nexusmods.com/skyrimspecialedition/mods/88261'>Terrain Blending Fix</a><br>
1. Under the 'Complex Parallax Materials' tab in the CS menu, make sure 'Enable Terrain' is checked.<br>
1. If on Skyrim VR, check if bLandSpecular is set to 1 in the SkyrimVR.ini file.
</p>
</details>


<details>
  <summary>

Parallaxed textures keeps enabling/disabling causing flickering!</summary>
  <p>

Make sure you have installed SSE Engine Fixes
  <details>
    <summary>

SSE Engine Fixes crashes the game!</summary>
    <p>

1. Install (Part 1) SSE Engine Fixes
1. Install (Part 2) Engine Fixes - skse64 Preloader and TBB Lib
1. Navigate to the location where (Part 1) SSE Engine Fixes is located and go in the folder skse/plugins
1. Open the file named "EngineFixes.toml" and set the following variables to false: _EnableAchievementsWithMods_, _SaveAddedSoundCategories_, _SaveScreenshots_, save and close the file
    </p>
  </details>
</p>
</details>

<details>
  <summary>

Strange bright lights / glowing smoke using <a href='https://www.nexusmods.com/skyrimspecialedition/mods/103219'>Extra Particle Lights Pack</a></summary>
  <p>Delete smokeparticle01.ini from Extra Particle Lights Pack</p>
</details>

<details>
  <summary>

The shadercache is not being generated / CS does not come in effect</summary>
  <p>For beta versions of CS the version numbering does not change. So CS cannot really see when it needs to update the shadercache. Usually deleting the shadercache is sufficient (step 2), but if you want to be on the safe side, also clean disk cache
There are two options to regenerate the shadercache:<br>
1. To regenerate disk cache you can open the CS in-game menu by pressing the end key and chose 'Clear disk cache'.<br>
1. To regenerate the shadercache open your 'overwrite' folder in MO2 as shown <a href='https://imgur.com/a/MOGhDmH'>here</a>. There should be a folder Shadercache with many subdirectories and .pso/.vso files in there. To let it regenerate with the next game start, simply delete the complete Shadercache folder.
</p>
</details>

<details>
  <summary>

Wetness effects look broken, have edgy areas and just completly wrong</summary>
  <p>Make sure that you have disabled Dynamic resolution in your Skyrim.ini [Display] section. It should be bEnableAutoDynamicResolution=0<br>
For nVidia users also make sure that in nVidia control panel the option 'Antialiasing - transparency' is set to off</p>
</details>


<details>
  <summary>

Tree LoD looks is too dark and looks strange</summary>
  <p>Earlier versions of CS had a special feature for tree LoD lighting which used "complex" texture with diffuse textures in the upper half and normal map texturs in the lower half. Therefore, it was suggested to activate this in Dyndolod. With the unified shader model, this is not required anymore. Hence if you have changed it earlier, make sure that you have set configuration value set like this: <br>
TexGen_SSE.ini:   TreeNormalMaps=0<br>
Dyndolod_SSE.ini: TreeLODComplexAtlas=0</p>
</details>