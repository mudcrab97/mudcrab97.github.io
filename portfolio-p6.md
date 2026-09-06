---
layout: default
---

{% include youtube.html id="KhlcLQ_UVzo" %}

# Spite: Threads of Fate
*As a fallen shieldmaiden denied Valhalla, you awaken in Helheim with nothing but your hammer and fury. Fight through the undead hordes in order to reweave your threads of fate!*

[<ins>itch.io</ins>](https://roadkill-interactive.itch.io/spite-threads-of-fate)

# Key Contributions

## Graphics Engine

For this project we were required to develop the game in a custom engine, and it was decided that the base of the graphics engine would be the one I developed in DirectX 11 during our graphics programming course. 

Much of my work was spent implementing new graphical features and improving the graphics pipeline.

Improving productivity is extremely important when working with such a short timeframe, therefore I spent a lot of my time implementing features such as shader hot reloading. This allowed our technical artists to see the results of their shader work immediately.

To allow for far distance shadows without sacrificing quality I wrote a simplified cascaded shadow maps implementation. This was later improved in the next project.

![](/assets/p6/1.webp)

## VFX and Particle Systems

VFX for me was all about creating a VFX pipeline that allowed our technical artists to create any effect they could think of. I made sure our material system had the flexibility needed to allow for any kind of shaders to be used on objects in the world. This allowed our artists to create interesting effects such as the player’s glowing ghost arm. 

To complement the carefully crafted VFX meshes I also implemented a particle system engine. Similarily with other VFX, I wanted this system to be easy to use and flexible. I implemented a suite of features that allowed a large variety of different particle effects to be created. 

The particle systems are defined in JSON files. While not as simple as a full editor, I still made sure the configuration is easy to understand. The JSON files are also hot reload-able, so the technical artists could see how each parameter effected the particle system in real-time. 

The particles themselves are simple billboard sprites. They are rendered as a point list, and the geometry for each particle is created in a geometry shader. The pixel shader for particles is exposed as a parameter, so our technical artists could write their own if they wanted something more interesting than static textures.

![](/assets/p6/2.webp)

## Audio Engine

For our engine’s audio implementation I decided to use the audio library BASS. I chose this over a more feature-rich library like FMOD for simplicity, as I have experience with BASS and most of the functionality we needed for the scope of this and future projects could be implemented by myself.

I wrote a custom 3D audio implementation with distance roll-off and stereo panning. Audio can be played as simple 2D one-shots, or by placing audio sources in the world.

![](/assets/p6/3.webp)