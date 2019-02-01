# Checklist for setting up lighting in Unity projects

## Choose a rendering pipeline

Rendering pipeline defines how GPU calculates the game world to be rendered to the screen. The actual scripts that define how each object in the game is rendered are called shaders. Shaders use a specific programming language called High Level Shader Language (HLSL), but on Unity’s new rendering pipelines, shaders can also be created with Shader Graph, a visual shader creation tool.

-	Default:
    - Good for all types of projects
    - When targeting low-end devices (e.g. mobile) can accidentally add too many features and game performance is slowed down
-	Scriptable Render Pipeline (SRP)
    -	New, still in development
    -	Will probably replace the old default pipeline eventually (in 2-3 years)
    -	Two built-in pipelines:
          -	Lightweight Render Pipeline (LWRP)
              -	For mobile, old consoles, Web etc.
              -	Good out-of-the-box settings
              -	Good performance
          -	HD Render Pipeline (HDRP)
              -	High-end consoles and desktop games
              -	All features
              -	VFX graph tool available
    -	LWRP and HDRP support Shader Graph, a visual shader building tool
    -	NOTE: old shaders written for default pipeline do not work with SRP!
          -	Conversion tool for shaders is available from the Unity menus for SRP, but conversion success is not guaranteed
          -	This means old shader available from asset store or tutorials may not work with SRP
          -	Also, do not try to convert an existing project to from default pipeline to SRP for these reasons!

## Choose rendering path

Rendering path defines technique the GPU uses for calculating lighting. Realistic looking lighting is a huge computational task, and game engines need to use different tricks and shortcuts in lighting calculations. Rendering path affects what is possible to achieve in lighting.

-	Forward rendering
      -	Default path, works with all devices
      -	Supports transparent materials
      -	Hard limits on the number real-time lights affecting any object
-	Deferred rendering
      -	Does not work on older devices
      -	No transparent materials!
      -	No hard limit on the number of real-time lights
      -	Shadow performance still limited by number of lights
      -	Use deferred rendering on projects where you need many moving lights, target new devices, don’t need transparent objects
      -	Example: Cities: Skylines uses deferred rendering to have moving vehicle headlights

## Choose color space

Color space defines how the luminosity of each pixel is calculated based on lighting and surface materials. Gamma is the default color space, but linear achieves a more realistic look.

-	https://docs.unity3d.com/Manual/LinearRendering-LinearOrGammaWorkflow.html
-	Color Space: Gamma
      -	 Works on every platform
      -	Produces “unrealistic results” with lighting in some cases
-	Color Space: Linear
      -	Ensures realistic lighting and mixing of colors
      -	Does not work on all platforms
-	Color space can be found from: Project Settings -> Player -> Other settings

## Skybox and ambient light set-up

A skybox is a six-sided texture that is rendered “behind” everything else in the scene. It is used to add depth to a 3D scene without needing heavy rendering calculations for geometry that is far away from the camera. Unity’s default skybox supports dynamic day-night cycle, as it adjusts the skybox based on the angle of scene’s directional light.

-	For each scene choose skybox if needed, otherwise set skybox to none
-	Choose ambient light (skybox/gradient/color)
-	Ambient light lights all objects in the scene the same way (no shadows)
      -	If you do not want any ambient light, set color to black

## Global Illumination

Term Global Illumination (GI) refers to all non-directional light in the scene, meaning light bouncing from one surface and affecting another surface, like a white wall bouncing sunlight to any nearby surface. Calculation of GI requires heavy computation and cannot be done real-time. Unity offers two solutions for pre-calculating GI.

-	https://medium.com/ironequal/a-lightning-fast-presentation-of-global-illumination-in-unity-5-112ab5e79c14
-	GI = pre-calculated indirect lighting in the scene
      -	Indirect light is light bouncing from surfaces and hitting other surfaces (e.g red wall bounces red light to nearby surfaces
-	Needs to be calculated (baked) into lightmaps
-	Lighting-tab:
      -	Choose one or both (Warning: choosing both can be very costly for performance!):
         -	Baked GI
              -	For static lights and static meshes
              -	Has Ambient Occlusion
         -	Realtime GI
              -	Also needs to be baked!
              -	Enables dynamic lights, but works still only on static meshes
              -	No ambient occlusion (can be added in post processing for desktop and consoles)
-	If you want GI to affect also dynamic(moving) meshes, use Light Probes
-	For reflections, use reflection probes
-	WARNING: dynamic(realtime) reflection probes are extremely costly for performance!
