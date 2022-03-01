---
title: Older versions
description: Mechanics Changes between Versions

# older versions

Why do people play non-latest versions?
If you are new to technical Minecraft you will probably find yourself wondering, why does the machine I saw on Scicraft not work on my [insert latest version] world? The reason itself is really quite simple, 
Scicraft is not running [insert latest version], they run 1.12.2.
 As well, contrary to popular belief, 
there are other technical servers
, and to top it all off, 
many of those also run 1.12.x
, with some even running more obscure versions including 1.7.2, 1.8.1, 1.13.1, 1.15.2, and more.

## Mechanics Changes by Version
### 1.16
- redstone dust behaviour changes
- mobs can only go one pack over the mobcap when spawning in a single tick
- villagers now save their golem spawning cooldown to NBT
- villagers no longer need to have worked in the past in-game day to spawn golems
### 1.15
- mobs despawning now happens the same way in lazy chunks as they do in entity-processing chunks
### 1.14
- iron farm/village mechanics drastically changed several times from 1.14-1.14.4, see the iron farming page
- new chunk loading level (in addition to lazy and entity processing): border, these can only process block events, block updates, and state updates
- chunks no longer get unloaded by the autosave every 900gt, instead all chunk loading tickets come with a timer specifying how long until they should unload.
- when an unloaded chunk is accessed, for example by a hopper or a chest, it is loaded as a border chunk for &lt;1gt
- when an entity goes through a nether portal, a 3x3 of chunks on the destination side is loaded as entity processing for 299gt, which causes the chunks around this 3x3 square and diagonal to the corners to become lazy loaded for that time, and the chunks around that 5x5 square and diagonal to the corners to become border loaded for that time
- spawning heightmap now checks for all blocks, instead of only light-obstructing blocks.
### 1.13
- spawning "heightmap" is per-block instead of per-subchunk
- mobs no longer spawn outside of the 128m radius sphere centred on the player
- dragon egg bedrock breaking removed
- RNG manipulation no longer possible (todo:why)
### 1.9
- block 36 now has a hitbox, removing the shifting floor mechanic
- piston no longer retracts an extended piston when de-powered in the same gametick
- string no longer breaks when the block below it is retracted
### 1.8
- LC value now decreases when skylight-obstructing blocks are removed from upper subchunks
- light updates no longer affect LC value
- the partial removal of block/item IDs causes some unused blocks to be removed

## Performance Changes by Version
### 1.16
- rails give out significantly less block updates
- block hitbox shapes are no longer checked individually for a "full top surface," and are instead cached
### 1.15
- redstone dust gives out significantly less block updates upon de-powering, as it now de-powers 2 levels at a time instead of 1
- Portals are now cached via POI which makes going through portals much faster.
### 1.14
- Lighting engine was moved to a seperate thread.
### 1.13
- "The Flattening" and related changes cause major performance drops across the board in relation to 1.12
- rails give out significantly more block updates
- block hitbox shapes are now checked individually for a "full top surface"