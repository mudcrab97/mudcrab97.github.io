# City of Darkness
*A blind monster prowls the abandoned city, standing between you and your freedom.*

*Can you sneak silently enough to escape?*

[<ins>Back</ins>](./)

# Key Contributions

## Portal Culling

When we chose to make a horror game set in a claustrophobic urban environment like Kowloon, I quickly got ideas on how we could improve performance and allow for more detailed environments. One of the first things I thought of was some form of occlusion culling, and since the environment consists of many rooms and corridors, I quickly landed on using portal culling specifically.

The idea with portal culling is to divide the world into rooms, and then place portals between these rooms. The world is then rendered recursively through these portals, clipping the camera frustum for each one. The frustum can then be used to cull objects, which ensures objects behind walls are not rendered.

This technique requires level designers to place rooms and portals, but that was done relatively simply by placing boxes in Unreal tagged either as rooms or portals. The engine then automatically created connections between them.

In the end, this added a lot to the game, as we could have very detailed environments without any loading screens between areas. Without this, we probably would have had to split the game into several levels, which could have been a bit immersion-breaking, something we wanted to avoid.

![](/assets/p8/1.webp)

## Audio Occlusion and Reverb

In horror games, atmosphere and immersion is everything, and a core part of immersing the player is the auditory experience. I wanted to boost the experience by implementing a more dynamic audio system that could occlude audio sources behind walls, and tweak their reverb based on the space they’re located in. 

For audio occlusion I implemented the ray-casting technique that is used in Remedy Entertainment’s Quantum Break. For every audio source 9 rays are casted towards the various points around the audio listener. The muffling of the audio is then dynamically adjusted based on how many rays were blocked. Lots of tweaking had to be made to handle various edge cases, but in the end it worked well to better spatialize 3D audio.

For the dynamic reverb I used a similar ray-casting technique. Every audio source cast rays towards all positive and negative unit vector directions which can then be used to calculate the volume of the space the audio source is located in in. The reverb is then adjusted based on this volume, further enhancing the dynamic audio.

![](/assets/p8/2.webp)

## Lighting Performance

Lighting is incredible important for crafting an unsettling horror atmosphere, so the engine had to be powerful enough to allow our level designers and artists to light the world exactly how they wanted. I had already made several optimizations to increase lighting performance in our previous project, but for this one I had to go even further, in some cases even completely re-writing how lighting worked. 

A major change was made with how lights were actually rendered to the screen. Previously the deferred lighting pipeline would draw all lights as screen-space quads, but for this project I instead used 3D meshes for point and spot-lights. The reasons for doing this is that it the graphics card will now automatically process less pixels for lights that take up little space on the screen, but it also means that we can utilize the depth buffer - meaning pixels can automatically be discarded if any pixels belonging to the light is behind any objects. 

Another major addition was the separation of static and dynamic shadows. Previously all shadows were rendered in the same pass for every light, every frame, but with this separation, shadows for static objects could be rendered separately. Thanks to this change, the amount of draw calls each frame lowered drastically, as static shadows only had to be rendered whenever objects or lights moved. 

With all of these changes added up, the engine could finally perform well enough to allow for any kind of lighting our team could think of.

![](/assets/p8/3.webp)