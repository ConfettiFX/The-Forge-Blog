---
layout: post
author: Kirill
date: May 25, 2022
title: Graphics programming from a content production perspective
---

When we implement a fancy new rendering feature we have to ask ourselves - what is the authoring workflow for that? How does this feature improve the production workflow?

Most importantly, does this feature make the production workflow worse? 🙂

Here is a small list of things we need to pay extra attention to:

* Does this feature require additional manual work to function properly?
* Does this feature substantially impact iteration or build times?
* Does this feature use perceptual or physically-based parameters for authoring?
* Is it actually worth it to ship and use this feature?

Now let us discuss these in more details!

### Does this feature require additional manual work to function properly?

Let us consider a global illumination system that require level designers to place boxes to markup indoor/outdoor locations on the level.

Now your level designers are doing mundane and meaningless tasks instead of, well, designing your levels. They get frustrated, team morale suffers and on top of that there is a significant ongoing dollar cost for that feature.

If this type of activity cannot be automated it has a huge negative value and high cost and should not be used in production.

### Does this feature substantially impact iteration or build times?

Lightmapping / precomputed indirect lighting immediately comes to mind 🙂

When it comes to lightmapping, I would like to reference this excellent paper: [GPU Driven Indirect Lighting in God of War](https://ubm-twvideo01.s3.amazonaws.com/o1/vault/gdc2019/presentations/Hobson_Josh_The_Indirect_Lighting.pdf).

The God of War team claim they can bake indirect lighting in less than 14 seconds on the PS4 devkit! The baking process is also interactive, so the artists who works with it can iterate instantly.

What is stopping us from implementing a lightmapper on the GPU? There's a perfectly suitable GPU on the market that has a 1Tb onboard SSD, it costs approximately $8000 USD at the time of writing this post.

That investment will pay off in a week, especially if the team is large.

We often spend all our attention on improving the runtime costs and neglect the build and iteration time costs. If a particular feature requires some form of precomputing, it is highly important to optimize the precomputing to be as fast as possible.

Iteration time is of paramount importance, because:
* It has a toll on team morale. People get frustrated and annoyed if they have to wait for the long builds, many often leave because of that.
* It costs a lot of money. Multiply the build or loading times by the hourly wage, then add an extra hour that is needed to get back into the "flow" - suddenly it's hundreds of thousands of dollars every year, or even more if the team is big.
* It's often one of the factors that lead to crunch. Low productivity means it takes more time to get the job done, but the deadlines stay the same.

We need to maintain a reasonable balance to ensure the team is happy and the game ships in time.

Another interesting topic is node-based material editors. In most cases long shader builds are caused by node-based material editor workflows. So, we have to suggest an alternative authoring workflow that does not generate 100k shaders.

Luckily, [Rendering the Hellscape of Doom Eternal](https://advances.realtimerendering.com/s2020/RenderingDoomEternal.pdf) describes a viable alternative!

Dooms rendering team implemented a clustered decal rendering system for their forward renderer, they have thousands of decals everywhere and only 350 pipeline states for the entire game.

Since these are forward decals, you can write custom decal blending code - for example make "wet" decals that only modify the roughness of the underlying surface or make "burning" decals that modify the alpha value and generate alpha-tested holes in geometry (something that deferred decals and d-buffer decals are not able to do).

Their forward renderer also allows to write custom shaders if you need them, with the idea that decals cover 80% of the authoring needs. In my personal experience, the two biggest use-cases for node-based materials are "general surface/material variations" and various graphical features that interact directly with the gameplay - decal-based workflow is perfectly suitable for the first part 🙂

It is important that decals is not the only alternative to node-based material systems. I believe that if we start to collectively think about better art workflows we can find other great ideas and improvements 🙂

### Does this feature use perceptual or physically-based parameters for authoring?

This falls into the "authoring tools usability". UI itself is usually the resposibility of a tools engineer, the parameteters that the UI exposes is on the graphics engineer 🙂

"Perceptual" parameter means that there is a direct and linear corellation between the parameter value and what the artist sees on the screen. It is the job of a graphics programmer to convert from perceptual or physically-based form into whatever internal representation is required under the hood.

The main reasoning for this is to have artists spend less time guessing and randomly tweaking these parameters and focus on what is important - authoring the content. 

### Is it actually worth it to ship and use this feature?

We have just evaluated the cost of using this feature in production. We roughly know how much time it would take to implement this feature.

Now the final question is - is it actuall worth the effort? 🙂 Is this feature absolutely central to the vision of the game and are the costs justified?

Thank you for your attention!
