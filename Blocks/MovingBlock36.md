---
title: Moving block 36
description: Mechanics around the moving piston block
---

# Moving piston block 36

Moving_Piston is commonly called a B36 or Block 36. This is because, before The Flattening<!--FIX: What's that?-->, it had the block ID 36. B36 is created by pistons when they push a block. The piston head also converts itself into a B36. B36 is non-solid<!--FIX:Seems to contradict the part where it says it has a hitbox--> and invisible, although it does have a hitbox.

## Uses
Special properties that B36 has which might be useful include:
- Preventing falling blocks from aging (falling blocks won't break while inside a B36);
- Having the Block Tags: dragon_immune, wither_immune;
- Entities can see through it but cannot pathfind through it;
- It can only be destroyed through an explosion;
- Being unreplaceable<!--What does this mean?--> by the player

## Hitbox
Like most blocks, the B36 has a hitbox. A B36 hitbox is very interesting as it can be offset from where the B36 is actually located. This can be seen by doing the following:
1. Go to the world border
2. Place a piston facing into the world border, with water on the other side.
3. Power the piston to extend it
4. Without unpowering the piston, break the it
5. Notice the invisible B36 on the other side of the border, and then walk into the invisible hitbox that is still there server-side

![b36hitbox](https://cdn.discordapp.com/attachments/793179568508108820/807318838139813948/WorldBorder_B36.png)
In this example, the hitbox stays in its original state, at the position it came from, because the tile entity isn't processed outside of the world border. The same thing happens in border-loaded chunks, for the same reason.  

When processed, the tile entity moves the hitbox and all entities colliding with it by half a block. Piston extensions are three ticks long, so this occurs three times. On the final tick, the B36 tile entities are turned back into normal blocks. That final tick can be forced to execute in the block event phase of a tick when a sticky piston tries to retract a B36, or a headless sticky piston retracts into a B36 with a movable block in front.

## Creating a B36
Creating a B36 without it turning into other blocks can be difficult. You can create a tile entity-less B36; only the block will be there, without a tile entity to turn it back into a block. Alternatively, you can prevent the tile entity from being processed by creating it outside of the world border or in a border-loaded chunk.

The usual method is using an explosion to blow up the tile entity in the same tick that a piston extends by using budded pistons, causing a tile entity-less B36 to be created in its place. It is no longer possible to create a tile entity-less B36 in 1.17+, as B36 data is now stored globally.
It can now be created with update suppression in the tick, causing a crash. This can be seen in a [Youtube video (https://www.youtube.com/watch?v=xXncCVzpg38)](https://www.youtube.com/watch?v=xXncCVzpg38)
A different setup can be seen below.
![simplified setup](https://cdn.discordapp.com/attachments/360084811408211988/935278303059775589/2022-01-24_22.00.57.png)

## Tile Entity Data
The following NBT tags can be accessed with the 
`/data get block` command:
- blockState: The moving block represented by this block entity.
- Name: The namespaced ID of the block.
- Properties: Optional. The block states of the block.
- Name: The block state name and its value.
- Extending: 1 or 0 (true/false) – true if the block is being pushed.
- Facing: Direction that the piston pushes (0=down, 1=up, 2=north, 3=south, 4=west, 5=east).
- Progress: How far the block has been moved.
- Source: 1 or 0 (true/false) – true if the block represents the piston head itself, false if it represents a block being pushed.
