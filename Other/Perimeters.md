---
title: Perimeters
description: What is a perimeter?
---

# Perimeters
A perimeter (or "peri" for short) in Technical Minecraft refers to a large area of blocks that has been cleared of its normal terrain, usually either by hand-digging or by using a world eater. Perimeters can be made with purely practical applications in mind, though it's common to build decoration (a "deco") to go along with it, usualy covering the floor, walls, and any farms or other builds inside the perimeter.

The shape of a perimeter (when viewed from the top down) is usually either square or rectangular, as those are the easiest ones to carve out with world eaters, though perimeters that are either partially or fully hand-dug are often more intricately shaped as to fit with the decoration. The name stems from the colloquial usage of "perimeter", which usually refers to the distance around the outline of a 2d shape, though in TMC it usually refers to the hollowed out space itself.

## Usage
Perimeters usually serve one or two purposes: to prevent mob spawning, and/or to reduce lag.

- Hostile Mobs in minecraft are able to spawn up to 128 blocks away from the player. If any spawned mob (that has not been made [persistent](https://minecraft.wiki/w/Spawn#Java_Edition_2:~:text=Mobs%20are%20persistent%2C%20meaning%20they%20do%20not%20despawn%20and%20do%20not%20count%20toward%20the%20mob%20cap%2C%20when%20they%3A)) ends up outside this rang, then they will also immediately despawn. Thus, clearing out an area of at least a 128 block radius will make sure no other mobs nearby will contribute to your mobcap. In order to allow for some leniency in afk spot placement, but still keep the size as a multiple of 16, it's common to choose a perimeter size of 272 blocks for hostile mob perimeters.
 - Do note that passive mobs do not despawn in the same way, meaning that for passive mob farms you would need a perimeter that covers the entire render distance.
- Perimeters can also be created as a lag reduction measure: clearing out the entire render distance around a player can reduce the amount of resources that the game spends on things like passive and ambient mob AI, the mob spawning algorithm, or any other causes of lag. These are commonly also found around builds that are quite performance-intensive or areas where the player will spend a large amount of time, such as a main storage system.

## Perimeters around non-mobfarm builds
The spawn chunks are a common place to see perimeters, as they will be loaded any time the overworld is loaded, meaning any performance saved here will give more leeway to any other farm that loads the overworld.

It is also somewhat common to see a small chunk grid around a nether portal [chunk loader](/pages/GameMechanics/ChunkLoading/) be turned into a mini-perimeter. While there may not be any player near enough to let hostile mobs spawn, there's still a chance of passive or ambient mobs wandering into the chunkloader's range, taking up space in their respective mobcaps.

## History
The first known perimeter to be constructed was on the ZipKrowd server. They wanted more efficiency from their quadruple witch farms, so they decided to remove all of the blocks around the AFK location, where other mobs may be able to spawn and fill up the mobcap. Perimeter creation has become significantly easier since the advent of movable TNT duping
