---
title: Mob Tick
description: Details on the tick method of specific mobs
---

# `tick()` Method of Specific Mobs

## The Villager

### `VillagerEntity.tick()`
1. super call to `tick()`
2. change `headRollingTimeLeft`
3. decay `gossip`

### `VillagerEntity.mobTick()`
Instantiate a `raid` variable which is an object from the `Raid` class
1. tick the brain
2. change `levelUpTimer`
