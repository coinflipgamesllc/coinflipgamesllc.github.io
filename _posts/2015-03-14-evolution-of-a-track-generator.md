---
layout: post
title:  "Evolution of a Track Generator"
date:   2015-03-14 09:00:00 -0500
categories: unity updates shuffle-grand-prix
permalink: /blog/shuffle-grand-prix/:title
---
Hey folks. It’s been a while and Shuffle Grand Prix has come so far along since the last update. Pictures speak louder than words, so I’ll just go ahead and post a quick screenshot:

![Dang son, that looks nice.](/assets/img/posts/sgp-screen-1.png)

<!--break-->

We’ve made a ton of updates to the gameplay, models and visual effects, but that is not what this post is about. This post is about the biggest evolution since the last blog update: the track generator.

I know, I know. We talk about that a lot. But really, it’s a great feature and something we’re really quite proud of.

So the question is: How do you generate an infinite set of random tracks that are valid, interesting and distinct? The answer was not something that came easily to us and not something we’re done answering. The progress we’ve made so far is quite stark, however:

![Track Comparison](/assets/img/posts/sgp-track-progress.png)

Along the way some design decisions were changed (several times), aesthetics were updated and models were created. We’re not at the end yet, but we’re *this* much closer.

The first version of the track generator was written in Lua using the excellent Löve framework. Shuffle Grand Prix was always destined for Unity, but Lua is the perfect prototyping language. We were able to get something up and running very quickly. The basic steps here were, actually, very basic (Tautologies, anyone?).

0. Generate some random points.
0. Calculate the convex hull of those points.
0. Push apart points where angles would be too tight.
0. Add additional, intermediate points and smooth curves.
0. Draw some random background terrain (we used [Diamond-Square](https://github.com/mlepage/heightmap/blob/master/heightmap.lua) at this stage)
0. Draw some lines to give the illusion of a surface.

That’s pretty much it. Nothing special, quite buggy and every track ends up with a sort of bean shape to it. The prototype was translated into C# and back up and running in Unity, but it was definitely not good enough! The second iteration was only slightly more complex:

0. Generate a random concave/convex shape. We used [this](http://stackoverflow.com/a/25276331/3220454) as the basis, but expanded on it extensively.
0. Push apart sharp corners and smooth, as before.
0. Create mesh along new set of points.
0. Collapse vertices that are close together.
0. Create a random, Diamond-Square terrain.

This was definitely an improvement, but still not great. The shapes, though concave were still mostly bean or flower shaped. The corners were particularly problematic due to mesh overlaps. Back to the drawing board…

The current method we’re using is a bit more complex and the results are much more varied and interesting.

0. Generate a random [voxel](http://en.wikipedia.org/wiki/Voxel) terrain using [Fractal Perlin Noise](http://en.wikipedia.org/wiki/Perlin_noise).
0. Place a [navmesh](http://en.wikipedia.org/wiki/Navigation_mesh) on top of the terrain.
0. Find a suitably flat area to place the starting points.
0. Start a random walk around the terrain using [A*](http://en.wikipedia.org/wiki/A*_search_algorithm).
0. Generate a [B-Spline](http://en.wikipedia.org/wiki/B-spline) between the points.
0. Fix intersections, overlaps, tight corners and steep hills.
0. Plop down some nice terrain decorations.

The results are, well…

![Track Preview 1](/assets/img/posts/sgp-track-preview-1.png)
![Track Preview 2](/assets/img/posts/sgp-track-preview-2.png)
![Track Preview 3](/assets/img/posts/sgp-track-preview-3.png)
![Track Preview 4](/assets/img/posts/sgp-track-preview-4.png)
![Track Preview 5](/assets/img/posts/sgp-track-preview-5.png)

You be the judge.