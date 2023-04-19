---
title: Entity transport
description: Mechanics used for Entity Transport with examples
---

# Entity Transport
Different ways to move entities

### Entity velocity
Every time the entity ticks, the entity moves by the entity velocity.
Entities have a friction coefficient (even when on air) of x0.99 or x0.98, which is applied to their velocity every time the entity ticks.
- Cramming adds a small amount to the velocity each tick
- Slime: Set velocity in the pushed axis to 1 block per second unless the velocity is already higher
- Explosion: Increase velocity in the direction directly away from the tnt

### Piston pushing  
Tile entity of moving pistons pushing entity, capped at 0.51 block per axis per gt

### Minecart snapping
A minecart will snap to a rail if its position is in the rail's block or the block above, it will move to a postion depending on the curve and slope of the rail, up to 0.75 blocks sideways with curved rail, 1.9375 block downward.

## Examples: 
- 10 bps piston conveyor: piston pushing 0.5 block/gt
- 20-30 bps piston and slime conveyor: piston pushing: 0.5 block/gt+slime: 1 block/gt velocity
- Piston bolt, 40 bps minecart deelevator: piston pushing: 0.5 block/gt + minecart sideways snapping: 0.5 block/gt
- Lazy entity launcher: cramming velocity accumulated while the entity is not ticking
- Tnt cannons: multiple explosions in 1 tick (or more if "lazy acceleration" is utilized).
