
{% include youtube.html id="YFa4Kuo_2O0" %}

# Grim the Gargoyle King
*As a gargoyle brought to life, embark on a crystal hunt. Charge through the shattered castle and find enough supporters to become king.*

[<ins>itch.io</ins>](https://roadkill-interactive.itch.io/grim-the-gargoyle-king)

# Key Contributions

## Instanced Rendering

Going into this project I had already come up with a laundry list of improvements I wanted to make to the engine, knowing what ended up becoming a problem in the previous project. Not only did I know where to focus my optimization work, but also how to improve the pipeline, and the rendering quality itself. 

The first of many improvements was the introduction of instanced rendering. In the previous project every single 3D mesh on screen had to be rendered as its own separate draw call. Since we had a fixed camera, frustum culling alone solved most of the performance issues, but as we now had a player controlled third person camera, this wasn’t enough.

I ended up implementing a instancing system that automatically batches meshes into instanced draw calls. This is done on level load, when it calculates a hash for mesh renderers based on their mesh and materials. This system ended up massively improving performance, and allowed us to populate the levels with a huge amount of similar objects. 

In the future I plan on improving the system even further, adding support for LODs, as triangle count turned out to be a bottleneck later in development.

![](/assets/p7/1.webp)

## Cascaded Shadow Maps

In our previous project I implemented the first iteration of cascaded shadow maps, however in that case all we needed was near and far plane cascade. Since we could see shadows from much larger distances this time I had to implement proper cascades. 

First I had to adjust my method of calculating a tight light projection to properly work with the free camera. Since our previous project was top down isometric we could get away with some hardcoded constants when calculating the near and far distances, but this time I wanted to implement a proper calculation. I do so by intersecting the light frustum with the world bounds, which will give me the required near and far distances to ensure that every shadow will appear as they should.

To reduce shimmering in shadow edges I also make sure that the light projection matrix is snapped to the nearest texel position.

With these methods we can now render 3 cascades for our directional light shadows, ensuring that the shadows maintain a high quality from far away. I also implemented blending between the cascades, to prevent any kind of visible seams in the transitions.

![](/assets/p7/2.webp)

## Point Light Optimizations

As more point lights were added to the levels I noticed that they had a major impact to the performance of the game. Since the engine is using a deferred renderer, lights were drawn as a screen quad and did all the complex lighting calculations for every pixel on the screen, even if the light only covered a small percentage of that screen. Running physically based lighting on every pixel on screen, for every light, was obviously quite inefficient, so improvements could definitely be made. 

First, I added an intensity check to the lights pixel shader. If a pixels light intensity falls below a certain threshold, then no lighting calculation will be done to that pixel. That way we skip all the complex PBL calculations if it’s not needed. 

Even with this new check added, though, all pixels on the screen still had to checked, which is unnecessary at this point since we know how large the point’s range is. The optimization we want now is to reduce the number of pixels the shader has to run on, and we can do exactly that with a scissor rect test. 

With a scissor rect we can essentially tell the shader exactly what pixels it should apply to. So with a point light we can calculate the screen region it covers by converting it’s world position and radius to screen space. Once we have that we can set a scissor rect and then draw the light as usual. 

All of these optimizations gave us large gains in lighting performance, allowing our level designers to populate the level with as many points lights as they desired.

![](/assets/p7/3.webp)