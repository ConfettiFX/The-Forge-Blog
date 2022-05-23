---
layout: post
author: Kirill
date: May 20, 2022
title: Workflow defines the product
---

There is a big problem in the game development industry that doesn't get enough attention, so I wanted to bring it up in this post 🙂

Most production workflows are terrible.

Artists, level designers and engineers often waste a tremendous amount of time "fighting" with the engine or the toolset. There is a popular idea that games have become extremely expensive to make, I believe that terrible workflows are playing a huge role in that.

### Known and unknown issues

Let's talk about two different groups of problems.

#### Obvious issues

This is a small set of examples from real projects that are "obvious" - people acknowledge them and actively work on solving them.
* It takes an hour to export the level and see the changes in the game.
* It takes 2 days to generate lightmaps for a single level and 2 weeks for all levels.
* It takes 3 days to build the shaders from scratch.
* It takes 6 hours to build the code from scratch and run the game.
* It takes 3 hours to build the code after changing one of the headers files.
* It takes an hour to boot the game in a debug build.
* It takes an hour to boot the level editor.

These are glaring and obvious issues, and most of the time there is some effort to solve them. Most of the time it's some form of distributed builds, which in my opinion is only delaying the inevitable.

Note that this is not an exageration, these are actual numbers from actual projects.

#### Less obvious issues

This is a more interesting list of issues, also from real projects. People are generally much less aware of them.
* It takes 10 or more actions in the UI to import a single texture into the game (or any other asset).
* The editor viewport uses a different renderer than the game.
* The team has to maintain a separate set of shaders just for the editor.
* The editor viewport does not run at interactive frame rate.
* The editor is not stable and often crashes.
* The editor does not allow to remap the hotkeys.
* The renderer does not use perceptual or physically-based parameters to define material or post process properties.
* Some of the renderer features require additional manual work to function correctly.

These are the things that the production teams (art, level design, etc) have to deal with daily.
What makes it less obvious is that people who use the toolset tend to not complain about clunky UI or editor crashes and find various workarounds instead.

Bad UI and poor usability can cost the team many days of wasted work. Every useless click that artists have to make, every time the editor crashes - this takes away precious productivity.

Iteration time is of paramount importance. Every hour that somebody needs to wait until the build is done or the level is exported - is at least 2 hours wasted.

Why is that so important?
* It has a toll on team morale. People get frustrated and annoyed by low productivity, many often leave because of that.
* It costs a lot of money. How much time does your team have to wait until the build is finished? Multiply that by the hourly wage, then add an extra hour that is needed to get back into the "flow" - suddenly it's hundreds of thousands of dollars every year, or even more if the team is big.
* It's often one of the factors that lead to crunch. Low productivity means it takes more time to get the job done, but the deadlines stay the same.

Please do not make bad editor UI 🙁

### Graphics engineering from a content production perspective

When we implement a fancy new rendering feature we have to ask ourselves - what is the authoring workflow for that? How does this feature improve the production workflow?

Most importantly, does this feature make the production workflow worse? 🙂

Let us select a few specific issues from the lists above and discuss them.

#### Some of the renderer features require additional manual work to function correctly

Consider a global illumination system that require level designers to place boxes to markup indoor/outdoor locations on the level.

Now your level designers are doing mundane and meaningless tasks instead of, well, designing your levels. They get frustrated, team morale suffers and on top of that there is a significant ongoing dollar cost for that feature.

If this type of activity cannot be automated it has a huge negative value and high cost and should not be used in production.

#### It takes 3 days to build the shaders from scratch.

Using node-based material editors, right 😃 ? In most cases long shader builds are caused by node-based material editor workflows.

Following our own advice, we have to suggest an alternative authoring workflow that does not generate 100k shaders.

Luckily, [Rendering the Hellscape of Doom Eternal](https://advances.realtimerendering.com/s2020/RenderingDoomEternal.pdf) describes a viable alternative!

Dooms rendering team implemented a clustered decal rendering system for their forward renderer, they have thousands of decals everywhere and only 350 pipeline states for the entire game.

Since these are forward decals, you can write custom decal blending code - for example make "wet" decals that only modify the roughness of the underlying surface or make "burning" decals that modify the alpha value and generate alpha-tested holes in geometry (something that deferred decals and d-buffer decals are not able to do).

Their forward renderer also allows to write custom shaders if you need them, with the idea that decals cover 80% of the authoring needs.

It is important that decals is not the only alternative to node-based material systems. I believe that if we start to collectively think about better art workflows we can find other great ideas and improvements 🙂

#### It takes 2 days to generate lightmaps for a single level

[GPU Driven Indirect Lighting in God of War](https://ubm-twvideo01.s3.amazonaws.com/o1/vault/gdc2019/presentations/Hobson_Josh_The_Indirect_Lighting.pdf).

The God of War team claim they can bake indirect lighting in less than 14 seconds on the PS4 devkit! The baking process is also interactive, so the artists who works with it can iterate instantly.

What is stopping us from implementing a lightmapper on the GPU? There's a perfectly suitable GPU on the market that has a 1Tb onboard SSD, it costs approximately USD$8k.

That investment will pay off in a week.

#### It takes 6 hours to build the code

Rewrite the code 🙂

Sorry I don't have an easy answer for it and it is probably already too late for an easy way out. Good luck!

Distributed builds does not fully solve this issue because:
* Linking is still serial and needs to happen locally.
* You still have to transfer everything over the network - even when everything is local in the office it is not instant and can become a significant bottleneck for everybody who is working from home.

### Final thoughts

It does not have to be this way 🙂

There is a saying "Workflow defines the product" - investing and building better production workflow will lead to happier teams, better games and less production costs.

Thank you for your attention!
