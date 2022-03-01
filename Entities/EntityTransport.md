---
title: Entity transport
description: Mechanics used for entity transport with examples
---

# entity transport

Different ways to move entities
1. Entity velocity
Applied every time the entity tick,you can accumulate entity velocity
Entities have a friction coefficient even on air,x0.99 or x0.98 applied every time the entity tick
- cramming
add a small velocity each tick
- slime
set velocity in an axis to 1 block per second unless the velocity is already higher
- explosion
add a velocity
2. Piston pushing
tile entity of moving pistons pushing entity,capped at 0.51 block per axis per gt
3. Minecart clip
A minecart will clip to a rail if his feet position is in the rail block or the block above,it will move to a postion depending on the curve and slope of the rail,up to 0.75 block sideway with curved rail,1.9375 block downward

Examples:
- 10 bps piston conveyor:piston pushing 0.5 block/gt
- 20-30 bps piston and slime conveyor:piston pushing: 0.5 block/gt+slime: 1 block/gt velocity
- piston bolt,40 bps minecart deelevator:piston pushing:0.5 block/gt + minecart sideway clip:0.5 block/gt
- lazy entity laucher: cramming velocity accumulated while the entity is not ticking
- tnt cannons: explosions in 1 or more tick if lazy acceleration