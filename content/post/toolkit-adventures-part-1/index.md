---
title: Toolkit Adventures - Part 1
date: 2024-07-10T19:46:43.724Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
I﻿n this series, I'll be writing my little stories of developing Toolkit engine. My main objective is to provide more information about my experience for job applications. Because even though we mention our previous co-workers in job applications, it's best to showcase what we did in our previous jobs and an open source projects helps this even more.



B﻿efore closing the opening part, I'm grateful **Cihan Bal** for this opportunity. It was a nice experience and I hope to work with you on a greater project where we both focus on without any other project/job in mind.

**TOOK [3](https://github.com/Oyun-Teknolojileri/ToolKit/commit/033f67a3f8942fff6e04f884c08f0fc41875ac15) - [4](https://github.com/Oyun-Teknolojileri/ToolKit/commit/2e4be9670d3895b59b8f2d0b225918cfae1a2bef)**

Editor had 2D and 3D viewport types but some code reusage made some inappropriate user interactions possible, so i had to cut or change some of them. For example; make some variables view-specific, add-remove some buttons for the view.



Grid had problems (wrong axises shown, cell size can't be changed, zoom is going to extreme or unwanted values). Most visible one was that grid had lots of aliasing, because of the line renderer. I tried to implement a simple version of The Machinery Engine's procedural grid renderer; so I removed some parts (finite grid support etc.) but as I didn't understand the shader at core, it wasn't a complete success. It had a lot iterations later in the project, but it was generally better-looking than previous line renderer.



Gizmo's interactions was hard to manage by the users. Most of them was simple but these 2 was the most important & hard ones to solve.

**Scaling**; big objects were getting bigger faster than smaller objects (scaling was AABB size related). By dividing the AABB's related axis by mouse delta, I fixed the issue. To understand better, i guess we can say it's similar to "multiplying by frame time" in gameplay programming.

**Rotation**; mouse delta was in world space, which means objects were rotating faster if we rotate them at a farther distance. Moved it to screen space to solve the problem.



**TOOK [152](https://github.com/Oyun-Teknolojileri/ToolKit/commit/6843b299365466bb39941539dcf3af9b2f489e75) - [118](https://github.com/Oyun-Teknolojileri/ToolKit/commit/227486d76c452f0d3c23469d4fbc916e3ee10177)**

Editor's importing process was done by an external process named **Import**. Before me, Import was serializing import files (.fbx etc) directly into Editor-ready resources. This is a bad design decision essentially if you have a resource-serialization system in the engine. So I decided to change direct serialization of Import to direct object filling. That means, I created resource objects user actively trying to import and then used serialization support of the engine. This decision took away all duality issues import system.

Long before me, there was a working animation system. But at some point, because they didn't need it immediately, it got a little bit old in the codebase and dropped dead. So I've been given the task of getting it back on its feet again. I was so happy because I hadn't write an animation system before. So I started by running the older version (where animation works) with old resources and see the differences between engine version to get which engine version broke the system.

It was mostly because animation resource format duality between the engine and Import as I mentioned before. So I fixed/updated all skeleton and animation serialization/deserialization code as modular as possible (using templates and function callbacks). For example; traversing each child is a function that takes a function as argumant, deleting/serializing a bone is different functions that can be passed to traversal function etc.



**TOOK [208](https://github.com/Oyun-Teknolojileri/ToolKit/commit/f89d9a188c9a4a4df05e4a34b73c5db9a4b8d36c)**

Previously the engine was using uniform buffers to pass bone information to GPU. But this limits the maximum number of bone that can be passed to GPU to 64. This number is pretty low for modern assets, so we wanted it to be as high as possible. Modern renderers/engines use storage buffers for this but OGL ES 3.0 doesn’t support it. There is only single way to pass big buffers to GPU in ES 3.0 and it is **texture sampling**. If we sample a texture with **filtering = nearest, mipmap = nearest_nearest settings**, we can access all of our floating numbers freely. So I separated our bone information into 2 textures; animation transform (dynamic) and bone bind pose (static).



There was a bug that if there are multiple skeleton meshes, one of them get glitchy. Sometimes, textures of some non-skeleton meshes were glitching too. Main reason was, engine hadn’t been designed for expandable renderer features. So I fixed it in TOOK 144, but it’ll be in my the next part of the series.



<!--EndFragment-->