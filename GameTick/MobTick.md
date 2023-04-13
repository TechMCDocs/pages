---
title: Mob Tick
description: Details on the tick method of specific mobs
---

# The `tick()` Method of Specific Mobs

## Villagers

### `VillagerEntity.tick()`
1. Super call to `tick()`
2. Change `headRollingTimeLeft`
3. Decay `gossip`

### `VillagerEntity.mobTick()`
1. Instantiate a `raid` variable which is an object from the `Raid` class.
1. Tick the brain
2. Change `levelUpTimer`
