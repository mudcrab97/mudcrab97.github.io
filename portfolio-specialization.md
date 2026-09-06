---
layout: default
---
![](/assets/specialization/introduction.gif)

# Procedurally Generated City

## Introduction

For my specialization project at The Game Assembly I decided to work with something I’ve had an interest in since my start in programming - procedural generation.

I wanted something large in scale rather than focusing on the small details, so a city was the first choice for me. This way I could focus on a handful of different methods of procedurally generating content.

The city is built completely from scratch, the only pre-authored assets are the shaders in use. Everything else, textures, road layout, buildings, and even the skybox, is procedurally generated using methods that range from simple rule-based generation to 3D Simplex noise.


This project was inspired the by the late [<ins>Shamus Young’s blog</ins>](https://www.shamusyoung.com/twentysidedtale/?p=2940).

# The Buildings

If you’re building a city there is always one thing you’re going to need - buildings. 

But where do you start when making a building? First, we’re going to need some textures.

## Texture Generation

Since we’re going to be focusing on modern buildings we really only need one texture to reach our vision. We need some windows. 

The low detail I’m going for means I won’t have to focus on minor details in the texture, but rather it has to look decent from afar. Generating windows on a grid is simple, but just white boxes won’t do. I also add some gradient to them, giving the illusion of light coming from the interior ceilings.

To make it appear as if there is an actual room inside I use some noise to generate shapes towards the bottom of the window. And finally I apply some random color noise over the whole window, adding a slight color variation to it.

The texture generation function has a few different options, allowing me to configure the look of the windows. I can change the size of the windows, the spacing between them, and I can even add a curve to the corners.

![](/assets/specialization/Windows.gif)

## Building Shapes

There are many methods available to generate the actual shapes of the buildings, but the most simple way is to come up with a few rules that decide what counts as a valid shape. Having full control over these rules makes it easy to configure the final silhouette of the buildings.

For this project I implemented two types of building types: Blocky and Cylinders.

The blocky type starts with placing one block with the maximum height and a random width and length. After that it randomly places a new block that intersects and is at least shorter than the previous one. Do this a few times and we end up with a somewhat convincing blocky building.

I complete the buildings by adding roofs to them, which can be placed as multiple shrinking tiers.

The cylinder type first places points in a circular pattern in steps of a constant angle, however while doing so it can randomly jump 90 degrees instead, giving the building angular shapes.

It then uses these points to create sections which it stacks on top of each other until it reaches its maximum height. We can also make the cylinder buildings more interesting by rotating the points as we move further up. This gives the buildings a twisting shape.

| Blocky building type                            | Cylinder building type                            |
|:------------------------------------------------|:--------------------------------------------------|
| ![](/assets/specialization/BlockyBuildings.gif) | ![](/assets/specialization/CylinderBuildings.gif) |

With the buildings finished we can now go the next step, which is to actually distribute them in a convincing pattern.

# The Road Network

## Generating City Blocks

Considering the scale and limited time for this project, any advanced kind of road network generation is out of scope. Therefore a block-based network is the most appropriate. We can create city blocks on a grid and then place simple billboard sprites to represent streetlights, which looks convincing enough from a distance.

Just a regular grid looks a bit boring to me though, as it gives way too many unobstructed sightlines far into the distance.

To make it look a bit more interesting I allow these city blocks to randomly grow, alternating the X- and Y-axis. The result of this is that we can have a large variety of differently sized city blocks, which not only makes the roads look more interesting, it also allows for different building sizes.

| City blocks in a grid pattern             | City blocks with varying sizes            |
|:------------------------------------------|:------------------------------------------|
| ![](/assets/specialization/roads+v1.webp) | ![](/assets/specialization/roads+v2.webp) |

After the blocks have been created we can then place building plots inside them. Building plot distribution follows a list of different patterns, a block can hold one large plot, or two long plots, or one long plot on one side with two smaller ones on the other, and so on… These patterns can then be rotated and flipped to allow for even more variation. After these plots have been placed, the buildings can finally be generated within those boundaries.

![](/assets/specialization/building+distribution.webp)

## Traffic

With the road network itself now looking decent enough, I wanted the city to look more alive. And what way of making a city come alive is there that’s better than adding some traffic?

![](/assets/specialization/cars.gif)

I found out early on that I had to set some rules for myself: The traffic system itself has to look convincing enough, but without any over-engineering. I could spend days building the perfect car AI, but what good is it if the cars themselves are just tiny little dots on the screen? Instead we’ll go for the most simple AI there is: Just go straight forward.

There is of course more to it than that, but for most of the driver’s lifetime they simply drive straight forward. But once they drive into an intersection, they follow some more complex behavior. When they do, they first make a choice. If they can continue straight forward, there is a 50% chance that they will. But if they can’t or the dice rolls in the other favor, they will randomly make a turn left or right.

When they decide to make a turn, they pick an end location and then create a path that they follow until they reach the end of it, after which they will continue going straight forward again. This path is generated by using a Bézier curve, so they drive through the intersection while turning smoothly.

![](/assets/specialization/intersection.gif)

The drivers have no regard for traffic laws when driving through an intersection so they regularly collide with each other, but does it really matter? The simulation is not made for looking at it from up close, it is made to look convincing from far away, which it does. I could have spent more time ensuring that the cars will avoid colliding with each other, but it would have done no favors for the look of the simulation, so I didn’t.

I still spent some time adding fun little behaviors, such as police cars driving around. It wasn’t a lot of work, and I think it added a little bit of extra polished detail to the city.

# The Sky

## Procedural Skybox

Now, the scene would look quite boring if it was just a city with a black backdrop, so lets make something that that will serve as the city’s background. 

I already lied somewhat by calling the chapter “procedural skybox” as we don’t actually need a full skybox texture. Normally we would use a cube map, but in this case we’re never going to look at the bottom or the top of the sky, so a simple 2D texture that we wrap around the world will do just fine. The most tricky part of generating this texture is that it has be tile-able. Otherwise, the it’s going to be very obvious when the texture reaches the end and wraps over to the start.

We start by adding a simple gradient fading from a top color to a bottom color with a bit of extra noise for some color variation. After that we use a high threshold on a value noise to serve as our stars. And then finally, we use some Simplex noise to add black clouds on top of everything, and this is where the tiling comes in.

So how exactly do we make this noise tile? Well, at first I simply tried mirroring the other half, but that looked completely awful. Then it struck me that instead of using 2D noise I can use 3D noise for this. That allows me to sample the noise in a circular pattern instead, which naturally makes it wrap around perfectly. And then as I move in Y on the texture, I move up in the 3D noise.

![](/assets/specialization/sky.webp)

# Conclusion and Future Improvements

From here on there are many improvements that could be made. I could add support for more building types. I could add more color and detail to the city by adding procedural billboards. I could add helicopters and blimps. A performance pass would definitely help a lot too, since right now my computer is starting to struggle with the simulation.

The point being that there is not really an end to what we could add to the city. What we have now is a great foundation and proof of concept.

## The Finished City

![](/assets/specialization/result.gif)