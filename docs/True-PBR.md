# True PBR for modders
PBR (physically based rendering) approximates how real materials react to light and is used in most modern games. True PBR is a (work in progress) part of Community Shaders that has several benefits:
* More realistic lighting than in vanilla and complex material is possible, and more effects such as sss, glow or multilayer can be combined.
* It allows the usage of PBR materials which are widely available online, many for free
* Those materials can be used easily in standard tools like Blender and exported in a simple way
* The materials will look the same way in Skyrim as in those tools so it is easy to create consistent visuals. There is no need to experiment and adjust brightness according to Skyrim's very unique levels

To use PBR you will need to obtain Community Shaders with True PBR feature (current dev build) and True PBR texture packs. Those are currently available only on the Discord. But remember, those texture packs will not work without Community Shaders enabled. If you are not a texture creator you can safely ignore everything else described below.


# True PBR for texture artists
All the followings sections are only relevant to you if are creating textures for Skyrim yourself. 
To generate a set of True PBR textures you need to do two things:
1. Update the meshes where those textures are being used to enable PBR for those meshes: True PBR only cares about meshes marked with a specific flag, everything else is rendered as usual. True PBR mods therefore contain meshes marked for PBR and a set of PBR textures. 
1. Create True PBR textures: The base textures for True PBR are albedo, normal, roughness, metallic, ambient occlusion and specular. Parallax and emission (if emissive color map is provided) are also supported. Additionally the material can use ~~two-sided foliage or~~ subsurface shading, which require subsurface color map and multilayer Parallax which approximates a semitransparent surface above a hard surface ([also following Unreal](https://docs.unrealengine.com/5.3/en-US/shading-models-in-unreal-engine/)).

## Mesh changes (in .nif)

### Flags 
The following flags of BSShaderProperty are used to tag a mesh for PBR rendering:
* Byte 55 (Unused01 in NifSkope) - enables PBR for this BSGeometry
* ~~Byte 57 (Soft_Lighting in NifSkope) - enables two-sided foliage shading~~
* Byte 58 (Rim_Lighting in Nifskope) - enables subsurface shading in NifSkope

For a mesh to be recognized as PBR, it must have in BSLightingShaderProperty:
* Default shader type
* In Shader Flags 2 have Unused01 (or PBR) flag set

### General Parameters
To tweak PBR settings for the mesh the following parameters of BSShaderProperty can be used:
* Specular Level (replaces Glossiness) - linear multiplier for nonmetal reflectance. Should be 0.04 for meshes which do not have specular map (and have 1 in alpha of RMAOS map) or 0.08 for meshes which have specular map created for Unreal.
* Subsurface Color (replaces Specular Color) - used in Two-Sided Foliage and Subsurface models. If subsurface map is provided it's used as linear multiplier for its RGB.
* Roughness Scale (replaces Specular Strength) - linear multiplier for roughness. The smaller the value the smoother and more reflective the texture.
* Subsurface Opacity (replaces Lighting Effect 1) - used in Subsurface model. If subsurface map is provided it's used as linear multiplier for its alpha. **Higher opacity makes the effect weaker - lower opacity allows more light through the surface.**
* Displacement Scale (replaces Lighting Effect 2) - linear multiplier for displacement.


### Batch mesh patching

To set up PBR meshes in batch, you can use [ParallaxGen](https://www.nexusmods.com/skyrimspecialedition/mods/120946). Patching is useful when retexturing vanilla meshes because there are thousands of them and users might have custom mesh additions or mesh fixes using the same textures. ParallaxGen now includes functionality of PbrNifPatcher and development will continue there. Read the original [PbrNifPatcher](https://github.com/ThePagi/PBRNifPatcher) page for the description of all available properties for patching.


### Viewing PBR properties in Nifskope (PBR Add-on)
To get descriptions for PBR properties and correct rendering of PBR in Nifscope, add [Nifskope addon for PBR](https://drive.google.com/file/d/1EjHnaV3p440eVkUaJaHgPOYFkluT3DTq/view?usp=sharing) into your Nifscope folder. Requires Nifskope 7, not the newer fork!


## Special rendering techniques

### Glint
Glint as a rendering technique refers to the simulation of small, bright highlights or sparkles on a surface, typically due to the interaction of light with fine details or textures. These highlights are often seen on materials with very small, reflective features like glitter, polished metals, or certain fabrics. Glint effects are important in creating realistic materials in computer graphics, especially in cases where the surface has microscopic variations that cause light to scatter and reflect in a particular way.

Parameters:

* To activate Glint use FitSlope flag
* to set glint parameters use MLP shader type  
* screen space scale is then in parallax inner layer thickness  
* log microfacet density is in parallax refraction scale  
* microfacet roughness is in parallax uv x  
* density randomization is in parallax uv y 
* nif.xml in [Nifskope addon for PBR](https://drive.google.com/file/d/1EjHnaV3p440eVkUaJaHgPOYFkluT3DTq/view?usp=sharing) has proper naming for parameters  

Glint is incompatible with coat or fuzz, but compatible with subsurface effects.

<details>
  <summary>Sample json configuration for NifPatcher</summary>

``` 
[
    {
        "texture": "landscape\\glacierslab", "emissive": false, "parallax": true, "subsurface_foliage":false, "subsurface": true,
		"emissive_scale": 0, "vertex_colors": false,
		"glint": 
			{
				"screen_space_scale": 2.0,
				"log_microfacet_density": 18.0,
				"microfacet_roughness": 0.7,
				"density_randomization": 200.0
			},
        "specular_level" : 0.02, "subsurface_color": [1,1,1], "roughness_scale" : 1, "subsurface_opacity" : 1, "displacement_scale" : 0.2
    }
]
```

</details>

### Fuzz 
Fuzz as a rendering technique is used to simulate the appearance of soft, fibrous materials, such as velvet, fur, peach fuzz, or certain types of cloth. This effect is crucial for rendering surfaces that have a fine layer of fibers or tiny hairs that interact with light in a distinctive way, producing a soft, diffuse reflection.
For CS PBR fuzz is still considered experimental and replaces specular term with another one, better suited to model cloth (especially velvet).

Parameters:
* enable soft lighting flag  
* to set fuzz parameters use MLP shader type  
* fuzz texture goes into slot 7 (right after rmaos). It has fuzz color in rgb (which is specular color of fuzz) and fuzz mask in alpha (0 for usual specular, 1 for fuzz specular)  
* fuzz color parameter is used as a specular color for fuzz if texture is not provided or as multiplier for color from texture. It's stored in first three parameters of MLP  
* fuzz weight parameter is used as fuzz mask if texture is not provided or as multiplier for fuzz mask for texture. It's stored in fourth parameter of MLP  
* nif.xml in [Nifskope addon for PBR](https://drive.google.com/file/d/1EjHnaV3p440eVkUaJaHgPOYFkluT3DTq/view?usp=sharing)contains proper naming for parameters  

Fuzz is incompatible with coat or glint, but compatible with subsurface. 

<details>
  <summary>Sample configuration</summary>

```
{
    "coatColor": [
        1.0,
        1.0,
        1.0
    ],
    "coatRoughness": 1.0,
    "coatSpecularLevel": 0.04,
    "coatStrength": 1.0,
    "displacementScale": 0.1,
    "fuzzColor": [
        0.0,
        0.0,
        0.0
    ],
    "fuzzWeight": 0.0,
    "glintParameters": {
        "densityRandomization": 2.128,
        "enabled": true,
        "logMicrofacetDensity": 12.87,
        "microfacetRoughness": 0.063,
        "screenSpaceScale": 0.0
    },
    "innerLayerDisplacementOffset": 0.0,
    "roughnessScale": 1.0,
    "specularLevel": 0.02,
    "subsurfaceColor": [
        1.0,
        1.0,
        1.0
    ],
    "subsurfaceOpacity": 1.0
}
```
</details>

### Hair model
Hair model shaders are specialized rendering techniques used to simulate the appearance and behavior of hair, fur, and other similar fibrous structures in 3D graphics.
CS PBR has hair model shading which is in experimental state.

Parameters:
- To enable hair model enable back lighting flag  
- no special textures, no special parameters  
- nif.xml in [Nifskope addon for PBR](https://drive.google.com/file/d/1EjHnaV3p440eVkUaJaHgPOYFkluT3DTq/view?usp=sharing) contains proper naming for flag  

Hair model is a completely different shading model so incompatible with anything else. Actual character hair is still unsupported at the moment.


### Multilayer Parallax (WIP)
Multilayer Parallax (MLP) is an advanced texturing technique used in 3D rendering to create the illusion of depth on a surface without the need for complex geometry. It enhances the standard parallax mapping technique by layering multiple textures, giving the impression of a detailed 3D surface with rich depth, such as the inside of a brick wall, deep cracks, or layered materials like soil or rock.

Parameters:
* Enabled by MultilayerParallax flag + MultilayerParallax shader type
* Coat strength - strength of coat layer contribution from 0 to 1. Equals to subsurface opacity field * alpha of slot 8 texture (if present)
* Coat roughness - roughness of coat layer. Equals to parallax inner layer thickness * alpha of slot 7 texture (if present)
* Coat specular level - reflectance of coat layer. Equals to parallax refraction scale (just set to 0.04)
* EffectLighting - enables diffuse contribution. Color of top layer is then taken from specular color field * rgb of slot 8 texture (if present)
* SoftLighting - enables interlayer parallax. I.e. with it enabled displacement works as displacement between upper and lower layer to simulate vanilla multilayer parallax
* BackLighting - enables own normal map for coat layer. It's then taken from rgb of slot 7 texture


<details>
  <summary>Sample json configuration for NifPatcher</summary>

```
[
    {
        "texture": "icicle", "emissive": false, "parallax": true, "subsurface_foliage":false, "subsurface": false, 
        "specular_level" : 0, "subsurface_color": [1,1,1], "roughness_scale" : 1, "subsurface_opacity" : 1, "displacement_scale" : 1.0,
		"multilayer": true,
		"coat_strength": 1.1,
		"coat_roughness": 1.0,
		"coat_specular_level": 0.02,
		"coat_diffuse": true,
		"coat_parallax": true,
		"coat_normal": true
    }
]
```
</details>

## Texture changes
### General textures
1. Base color in RGB, opacity in A.
2. Normal in RGB, A unused.
3. OPTIONAL Emissive color in RGB, A unused.
4. OPTIONAL Displacement in R, GBA unused.
5. Unused.
6. RMAOS - Roughness in R, metallic in G, AO in B, specular in A.
7. OPTIONAL Multilayer normal in RGB, roughness in A.
8. OPTIONAL Subsurface color in RGB, subsurface opacity in A. Multilayer coat color in RGB, opacity/strength in A.

An overview for PBR and non-PBR textures can be found [here](https://docs.google.com/spreadsheets/d/1WJaqIXpk44ISA2bFxi1a054JQD26tFqF-thDC7mHesc).\
If the actual colors are sRGB (gamma in Mixer), use sRGB flagged dds. If the actual colors are linear, use linear dds. 

### Landscape textures
Landscape texture is assumed to be PBR if a json file with the same name as the texture set record's EDID is found in 'data\PBRTextureSets'. For 'LandscapeDirt02 [TXST:00000C0F]' it would be 'LandscapeDirt02.json'
The texture paths are defined in the texture set record:
   * TX00: 'PBR\Landscape\Dirt02.dds' Base color in RGB (assumed to be in SRGB format), transparency in A.
   * TX01: 'PBR\Landscape\Dirt02_n.dds' Normal in RGB, A unused.
   * TX02: 'PBR\Landscape\Dirt02_rmaos.dds' RMAOS - Roughness in R, metallic in G, AO in B, specular in A.
   * TX03: 'PBR\Landscape\Dirt02_g.dds' Glow in RGB, A unused. (assumed to be in SRGB format) (unused for landscape)
   * TX04: 'PBR\Landscape\Dirt02_p.dds' Displacement in R, GBA unused. (use a greyscale image and save as BC4)
   * TX05: Unused
   * TX06: 'PBR\Landscape\Dirt02_crn.dds' Coat normal in RGB, coat roughness in A. (only used for multilayer parallax - not used for landscape)
   * TX07: 'PBR\Landscape\Dirt02_s.dds' Subsurface colour in RGB, subsurface opacity in A (assumed to be in SRGB format); OR coat colour in RBG, coat strength in A (assumed to be in SRGB format) (both unused for landscape). Only used if the mesh has flag set "subsurface": true. Otherwise the texture is not needed.
	
Usually, your record will look like this for landscape:
* TX00: 'PBR\Landscape\Dirt02.dds'
* TX01: 'PBR\Landscape\Dirt02_n.dds'
* TX02: 'PBR\Landscape\Dirt02_rmaos.dds'
* TX03: -
* TX04: 'PBR\Landscape\Dirt02_p.dds'

The json file contains the values that are normally defined in the mesh:

    {
        "roughnessScale" : 1.0,
        "displacementScale" : 0.25,
        "specularLevel" : 0.04,
        "subsurfaceColor" : [1.0, 1.0, 1.0], (currently unused)
        "subsurfaceOpacity" : "0.1" (currently unused)
    }
You can tweak the values to your needs, but be aware that landscape textures get also applied to a lot of meshes via texture set swaps - dirtcliffs, for example. In those cases, the settings in the mesh are used (and it also needs to be set up for PBR). But because many different textures are used on these meshes, your landscape textures should use similar values for parallax scale for your landscape textures. A texture that's made for a scale of 0.1 being applied to a mesh with a scale of 0.25 will have much stronger parallax than you intended.

If any of the textures in a quad is PBR, then the entire quad is rendered as PBR. So in order to avoid issues with blending between PBR and non-PBR textures, all landscape textures in a worldspace need to be converted to PBR together. That is to be okay since mods replacing land textures usually replace them all. Also, landscape LOD will not be rendered as PBR (blends together in the distance).

### Texture Creation Workflow

Many programs can be used to work with PBR textures. To generate textures from scratch, you can use for example Substance Designer or Material Maker (free). To edit, mix and apply materials to meshes, you can use Quixel Mixer (free), ArmorPaint (free) or Substance Painter. In this short guide I will show the workflow I use to retexture vanilla meshes.
The following workflow is based on [Quixel Mixer](https://quixel.com/mixer) and [Chainner](https://github.com/chaiNNer-org/chaiNNer/releases):
1. When installing Mixer, you can select the sample packs, which contain textures you can freely use. Do NOT select the Unlimited Megascans option - it is only free if your end product is in Unreal Engine. Though do inform yourself, I am not a lawyer.
2. Get some free PBR textures for example from polyhaven.com or ambientcg.com, or make your own. Import them using Library -> Import Custom Asset.
    * Make sure to fill out the correct albedo - one is for specular and one for metalness workflow. Most available textures use metalness workflow.
    * Make sure to use the correct normal map - Mixer uses the OpenGL variant. If you use the DirectX variant, normals may look wrong in Mixer.
    * You can add texture names to autofil in Edit -> Configure Autoload Map Names
3. Edit and mix textures however you want. Mixer has official tutorials on Youtube.
4. Export the PBR textures. You can create a preset that exports according to your needs.
    * Pick image format according to preference. I use PNG.
    * Export albedo as _Albedo.png, RMAOS as _RMAOS.png, normal as _Normal.png, and displacement textures as _Displacement.png.
    * Take note if you export albedo in Gamma or Linear space. This will be important later.
    * You can directly export a RMAOS (roughness, metallic, ambient occlusion, specular) texture. In most cases you won't need specular and can save as RGB. 
    * If you indeed worked with opengl normal maps, set Normal Y (green channel) to be inverted. This transforms it into DirectX format which Skyrim uses.
5. Use Chainner and load the following [workflow](https://drive.google.com/file/d/1S3XEIvxdIhfwHSiIg3ZdylpIZdhhVbWx) to automatically convert the textures towards the structure Skyrim needs them. In the workflow on the left side select the directory where you exported the textures via Mixer and enter the name used for the output.\
Please note, if you exported your albedo as Gamma, you need to select compression with sRGB. This is the 'Save Image' node in the topright corner of the workflow.\
Optionally change percentages of Resize nodes to downscale your textures before saving.






# FAQ
<details>
<summary><b>What about Complex Materials?</b></summary>
Complex materials are totally separate from True PBR and there would be no point in combining them. You can still use complex materials on non-pbr objects as usual.
</details>

<details>
<summary><b>Can I convert vanilla shaded textures to True PBR?</b></summary>
If you originally had PBR textures that you somehow converted for Skyrim, you can use those easily. Otherwise it is impossible to do without assumptions, because Skyrim textures contain fundamentally different data. For example, many objects using enviornment mapping are metallic, but not all of them. You can do manual work or use some AI to make the necessary textures, but it is unlikely that it will be a meaningful visual improvement over vanilla.
</details>

<details>
<summary><b>My materials ingame look wrong?</b></summary>
There is many possibilities for this, I will list the common ones. If the material is really shiny, the mesh probably isn't marked for PBR, so is vanilla shaded with specularity 1. If the material is too dark in the shadows, you might have too dark ambient occlusion. If the material gets black when you walk to it with a torch, your normal maps might be wrong - either flipped, or wrong format (opengl).
</details>

<details>
<summary><b>About Texture Compression</b></summary>
There is a number of compression format for DDS textures with various properties such as number of channels, quality and file size. Not all of them are supported by Skyrim. The following list should be sufficient in most situations:

* BC1: RGB + 1bit alpha (fully opaque or fully transparent). It has reasonable file size and quality. Use for opaque or cutout albedo textures, and RMAOS textures if you don't need specular. In case of visible banding or squarish artifacts, use BC7.
* BC4: One channel (grayscale). Has the same file size as BC1, but only one channel with better quality. Use for parallax textures.
* BC7: RGBA. Has good quality and twice the file size of BC1. Use for normal maps and albedo with full transparency.
* BC3: RGBA. The RGB part uses BC1, alpha uses BC4. The file size is the same as BC7. This can be useful when you need to preserve a quality alpha channel, for example in complex materials or landscape textures with parallax in alpha channel.

For more details I recommend [this article.](https://www.reedbeta.com/blog/understanding-bcn-texture-compression-formats)
</details>
<details>
<summary><b>Linear vs sRGB because it bears repeating</b></summary>

* You can find discussions online about linear, sRGB, non-color data etc. The details are above my paygrade, here we just hope that stuff works.
* Extremely simplified: Math is easier with linear data, but using sRGB files allows them to store more detail human eyes can actually see (this only makes sense for colors).
* All non-color data such as normals or roughness should be flagged as linear when saving to dds.
* Color data is more complicated - you need to know whether the data (the colors) itself is in sRGB or linear space. AFAIK, albedo/diffuse textures from sites like polyhaven are in sRGB and it is more common on general. If this is the case, you should use the sRGB flag when saving to dds. The flag will allow to the texture to be converted to linear automatically on the gpu. If your data (the colors) is already in linear space, use the linear flag when saving to dds. As you can imagine, if you use the wrong flag, the texture will either be superfluously transformed to linear or not transformed when it should be, which will cause the texture to look wrong.
* BUT WAIT! That applies to True PBR. If you are making textures for vanilla Skyrim... make sure your data is sRGB and use the linear flag. WHAT? Skyrim uses linear-flagged dds that actually contain sRGB data. I'm not sure if they convert the colors to linear manually in shaders or just calculate lighting in sRGB space. So yes, it is weird but it works this way (blame Bethesda).

</details>


<details>
<summary><b>Do I use rmaos or rmao textures?</b></summary>

You use the specular layer for surfaces like glass, gems, perhaps snow. If there is only one type of surface in your texture you can use rmao and set the specular scale in the nif/json configuration. If you have different surfaces with specular properties, only then it makes sense to use rmaos with a specular map in the alpha channel.

</details>


<details>
<summary><b>What is the best way to adjust parallax scale?</b></summary>

If you want to change the parallax scale of your texture because it looks to flat instead of changing the displacement map better try to adjust parallax scale in the json for the nif patcher. This usually creates good results.

</details>


<details>
<summary><b>Is there any best practice how to define the specular maps and strength?</b></summary>

Most materials should use the default value of 0.04 (in the mesh & json settings). That means your alpha would be white and you can save the texture as BC1. Snow has half the specular strength of most other stuff, so you could use a full 128 grey image for the alpha channel or reduce the specular strength in the json & patcher files to 0.02
</details>