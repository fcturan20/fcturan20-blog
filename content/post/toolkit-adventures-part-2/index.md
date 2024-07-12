---
title: Toolkit Adventures - Part 2
date: 2024-07-12T01:23:45.505Z
draft: false
featured: false
image:
  filename: featured
  focal_point: Smart
  preview_only: false
---
**[TOOK 126](https://github.com/Oyun-Teknolojileri/ToolKit/commit/64e963ffdad6bee9e0cc36ba28fa9cf2dace0373):** [](https://github.com/Oyun-Teknolojileri/ToolKit/commit/64e963ffdad6bee9e0cc36ba28fa9cf2dace0373)



I implemented an animation playing component in this commit. So I should explain the engine’s way of implementing Entity and Component system.



Game engines that has an user-friendly editor should have a in-editor system to add variables to entities. With this way, user doesn’t have to write a new editor window for each entity. Also, user should be able to call some functions (playing an animation etc.) to preview the data. In Toolkit, they’d implemented this before me by having 2 different systems; ParameterVariant/Block & Component.



**ParameterVariant (PV)**: This is a data-serialization only system. A PV should only have variables, it shouldn’t have any functions. It’s only to store data and serialize/deserialize it. So we can easily say that it’s the smallest building block of data in Toolkit. C++’s default std::variant library is used in this system. They can be displayed/edited in editor and Editor implements how each PV is shown in UI.\
**ParameterBlock (PB)**: Multiple PVs can be stored in a PB. PB provides functions to manage/access PVs easier. Every struct that’s inherited from **Object** in Toolkit has a PB, that makes every object serializable by default (if it obeys the engine’s code design). A PV can be added into a PB either by code (TKDeclareParam macro) or in run-time. But PBs have dynamic list of PVs, meaning there is no guarantee that a PV will always exist if it’s added by code.

**Component**: A system every engine has. In Toolkit, they decided to use PBs to store&serialize generic data and display/edit the data in editor. If a component should have a run-time/gameplay-time only data (active playing animation pointer etc.), it shouldn’t use PB system and directly store it as a C++ struct variable. Editor implements how each Component is shown in UI too.



We wanted play animation in editor without needing to program it into the game. So I decided to create an animation playing component (ACC). An ACC stores active playing animation and a list of possible animations (last played frame etc.). We want to serialize animation list, but not active playing animation so I created a PV called AnimRecordPtrMap. I decided to do this because I wasn’t very sure about how PBs work and whether it is better for engine’s code design. Also; if there is one animation record in any place, it’s likely that there’ll more than one.



**Note**: Now by looking at these, I think it would be better to create a PV to store single AnimRecord and a PB to store multiple AnimRecords but I’m still not sure whether that would fit engine’s code design.



I also fixed some small bugs such as; serialization utility functions were printing wrong uint values (strtoull-> stdoull & atoi), selected object rendering fixed by setting texture slot.



**[TOOK 144](https://github.com/Oyun-Teknolojileri/ToolKit/commit/4e6e70418660647613bda7eb963eadb02190d9e7):** [](https://github.com/Oyun-Teknolojileri/ToolKit/commit/4e6e70418660647613bda7eb963eadb02190d9e7)



As Toolkit only support OGL 3.0, engine had no graphics api backend. All OGL functions are called in render pipeline, which makes tracking API calls harder. Most of the bugs we had was because of wrong texture slots bindings. I decided to implement a SetTexture() function in renderer to keep track of bound textures and moved all **glActiveTexture()** + **glBindTexture()** calls to GetRenderer()->SetTexture() calls. Previously all sampler uniforms in shaders had custom names such as diffuseTexture, but I decided change this as s_textureX. This approach makes it difficult to understand shaders but it forces the developer to provide a documentation, which Toolkit needed a lot.



Engine with complex render pipelines (custom surface shaders with different texture inputs) would use more complex SetTexture() implementations but at the time, Toolkit didn’t target to be that complex. So I left it as is.



I also fixed some skeletal mesh memory leaks and delete behaviors.



TOOK [241](https://github.com/Oyun-Teknolojileri/ToolKit/commit/bb6b9df9769e68bc4dd4db3c7f6b2deace66a54a) & [45](https://github.com/Oyun-Teknolojileri/ToolKit/commit/b0f6ce2d7f4013453205b2ecae28974f677af266):



Toolkit’s Editor didn’t have resource manipulation tools (changing a mesh’s materials) and entity space tools (for example, Unreal’s Object window). So when user wants to create an entity with multiple mesh parts with specific locations, they have to create an empty scene (and it will be a Prefab scene) and merge it with the current scene. Also this arises the question of how to merge, should we directly copy Prefab scene into game scene or should we instantiate so our changes in the the prefab scene directly applies to game scene.



Toolkit had single way of merging scenes; direct copying. Copying is simple, but sometimes user wants to change one single parameter of multiple entities (material of mesh, available animations etc.). So copying is not enough in terms of usability. That’s why I implemented instantiating as Prefab scene merging in **TOOK-45**.



First step was to fix entity ID system’s bug in **TOOK-241** because scene’s first entity’s ID had deserialized ID instead of having ID=1. This had the issue that if multiple scenes are merged together & entities deleted regularly, entity IDs were skyrocketing even though total entity count doesn’t change at the end. So I changed it to first entity of a scene always has ID = 1. When scene A is merged into scene B, scene A’s all entity IDs are offsetted by scene B’s max entity ID.



Toolkit already had an entity instance system but it was behind a key combination and it had problems such as deleting a PV or component from instance was directly affecting the main entity. By adding a new PV called IsInstance into base entity class, we could easily check which changes can be done to the entity. For example; if an entity is an Entity Instance, it doesn’t have component list. Each access to its components returns a list of components from the base entity.



**Note**: I forgot to set PB as uneditable as we didn’t use it as much. But we already removed the entity instantiating system as I implemented Prefab system.



Prefab system was all about loading a scene into memory, instantiating every entity there and adding them into game scene as child of **Prefab Entity** while respecting the prefab scene’s entity hierarchy (root entities of prefab scene are childs of prefab entity). Prefab entity’s child entities are all instantiated, but we didn’t want them to have different PBs from base entity’s. So i had to fix PropInspector accordingly. First, i changed it so that it displays all of the childs PVs in Prefab Entity’s properties under PrefabData category. Then I disabled add&remove PV buttons in the category to only allow display&change the values of PVs.



There were small additions on all over the editor and engine such as selection system changed to select the whole prefab when a child is selected, delete actions deletes all prefab entity etc.