---
title: Toolkit Adventures - Part 3
date: 2024-07-16T21:53:20.531Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**TOOK [304](https://github.com/Oyun-Teknolojileri/ToolKit/commit/785cc9acbd040f49bcdf8078d4ab03ab42b6d213) & [305](https://github.com/Oyun-Teknolojileri/ToolKit/commit/40ce22962a6fe751865193d2cdfbe4daa49fcc1d) & [232](https://github.com/Oyun-Teknolojileri/ToolKit/commit/988644aacfa008ea73d4550b43da4bc23c896692):**

Even though AnimControllerComponent is implemented, we had to copy the skeleton resource in memory for each skeleton mesh playing because skeletons was storing active world transformations of the bones. To fix this, I’ve implemented SkeletonComponent. Skeleton Component stores a skeleton and a list of world transformations (DynamicBoneMap) that’s created according to the active skeleton. With this way, I also removed a run-time only information from a resource in **TOOK-305** which helps the engine’s code design.



When a skeleton mesh is added into an entity, animation system looks for a skeleton component to get the pose from. If skeleton component isn’t found in the entity, animation system uses skeleton mesh’s default skeleton resource. Each skeleton resource also stores its T-Pose as DynamicBoneMap. So there is no glitch even when enough data isn’t provided by the entity.



Assimp imported scenes always share single skeleton and this feature was messing our Import tool because it was trying to import multiple meshes as different resources pointing to same skeleton. So I changed the tool so that, multi-part skeletal meshes are merged into single skeleton mesh in **TOOK-304**.



There were also 2 mesh rendering path; skeleton and non-skeleton. But they were the same except skeleton one had 2 extra textures to bind. So I merged the paths in **TOOK-232**.



**TOOK [286](https://github.com/Oyun-Teknolojileri/ToolKit/commit/817b9dcf3d09ef884172729dd25532b979b5bf58):**

First iteration (I mentioned it in the first post) was looking good on my 32” 2K monitor but my coworkers had main axis lines invisible and aliasing all over the place. Original shader of The Machinery was lost at the time (as the engine gone private), so i had to modify the shader on my own.



First of all, shader’s main algorithm is: Each pixel is in the grid’s one of squares. No matter how far the pixel is from the camera, we should see squares denser at grazing angles (higher LoD). So we’re using screen space derivatives of the grid’s uv to calculate that. Grid’s uv is just world space size of the grid, so derivatives gives us the world space distance between pixels. Then depending on the grid’s cell size, calculate 3 nearest LoD squares and pixel’s distance to them. Nearest one is always gonna have the thinnest, second one has thicker and third one has the thickest lines. There is a problem and if we go far enough, each pixel may correspond to a square. Algorithm fixes this by using LoD information as alpha channel.



I fixed the antialiasing issue by parameterizing the algorithms lineMaxPixelCount parameter (it was fixed to 2 pixel before).



Most of our problems was on 2D viewport, because it had orthographic projection and our distances were jumping much more faster than 3D viewport which made transition looks worse. And some distances were producing thin lines but our users didn’t need them (main thick lines were enough). As a fix; I removed alpha multiplication for pixels in 2D viewport. It removed all of the transition and there were no issues with it because our camera distance was always in fixed sizes in 2D viewport.