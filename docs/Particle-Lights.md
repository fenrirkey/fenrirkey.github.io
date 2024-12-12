# Particle Lights

# Background
Community Shaders is able to process particle lights that were originally created for ENB. See
[Guide to adding particle lights](https://www.nexusmods.com/skyrimspecialedition/articles/1391)

# Adding Particle Lights
While you can still edit meshes, CS actually allows adding particle lights by creating an ini file.

Particle lights may be added by creating a .ini file in the `\data\ParticleLights` directory.

The name must match the name of the texture. For example, `fxglowenb.dds` requires [`fxglowenb.ini`](https://github.com/doodlum/skyrim-community-shaders/blob/main/features/Light%20Limit%20Fix/ParticleLights/fxglowenb.ini)

As long as the file exists, it will be used. The content can have the following values; any missing get a default listed below..
```ini
[Light]
Cull = false
ColorMultRed = 1.0
ColorMultGreen = 1.0
ColorMultBlue = 1.0 
RadiusMult = 1.0
SaturationMult = 1.0
Flicker = false
FlickerSpeed = 1.0
FlickerIntensity = 0.0
FlickerMovement = 0.0
```

More examples.
* https://github.com/doodlum/skyrim-community-shaders/tree/main/features/Light%20Limit%20Fix%20-%20Candle%20Glow/ParticleLights
* https://github.com/doodlum/skyrim-community-shaders/tree/main/features/Light%20Limit%20Fix%20Particle%20Lights%20Example/ParticleLights
* https://www.nexusmods.com/skyrimspecialedition/mods/99768