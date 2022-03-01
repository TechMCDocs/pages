---
title: Moving piston block 36
description: Mechanics around the moving piston block
---

# moving piston block 36

Moving_Piston is commonly called a B36 or Block 36 in the technical community. This is because, before The Flattening, it had the block ID 36.
B36 is created through pistons. When a piston pushes a set of blocks, it transforms all blocks in that set into B36 while they move. The piston head also converts itself into a B36. It is non-solid and invisible, although it does have a hitbox.
## Uses
Special properties that B36 has which might be useful include:
- Preventing falling_blocks from aging (falling blocks won't break while inside a B36);
- Having the Block Tags: dragon_immune, wither_immune;
- Entities can see through it but cannot path through it;
- Being destroyed only through an explosion;
- Being unreplaceable by the player
### Hitbox
Like most blocks, the B36 has a hitbox. A B36 hitbox is very interesting as it can be offset from where the B36 is actually located. This can be seen if you do the following:
Go to the world border
Place a piston facing into the world border, with water on the other side.
Extend the piston
Without unpowering the piston, break the piston
Notice the invisible B36 on the other side of the border, and then walk into the invisible hitbox that is still there server-side

![b36hitbox](https://cdn.discordapp.com/attachments/793179568508108820/807318838139813948/WorldBorder_B36.png)
In this example, the hitbox stays in its original state, at the position it came from because the tile entity isn't processed outside of the world border. The same thing happens in border-loaded chunks, for the same reason.
When processed, the tile entity moves the hitbox and all entities colliding with it by half a block. Because piston extensions are three ticks long, this occurs three times. On the final tick, the B36 tile entities are turned back into normal blocks. That final tick can be forced to execute in the block event phase of a tick when a sticky piston tries to retract a B36, or a headless sticky piston retracts into a B36 with a movable block in front.
## Creating a B36
Creating a B36 without it turning into other blocks can be complex. You can create a tile entity-less B36; only the block will be there, without a tile entity to turn it back into a block. Alternatively, you can prevent the tile entity from being processed by creating it outside of the world border or in a border-loaded chunk.

The usual method is using an explosion to blow up the tile entity in the same tick that a piston extends by using budded pistons, causing a tile entity-less B36 to be created in its place. It is no longer possible to create a tile entity-less B36 in 1.17+, as B36 data is now stored globally.
It can now be created with update suppression in the tick, causing a crash:
https://www.youtube.com/watch?v=xXncCVzpg38
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