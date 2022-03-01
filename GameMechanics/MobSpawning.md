---
title: Spawning algorithm
description: Description of the spawning algorithm
---

# spawning algorithm

## LC Value
LC value represents the height up to which the spawning algorithm distributes spawns within a 1x1 column of blocks in the world. The LC value is determined by the y level of the highest non-air block in the column (In 1.12.x and below it was the y level of the highest skylight-obstructing block, ceilinged mod 32, minus one).
The spawning algorithm gets a set number of spawn attempts to distribute amongst all the blocks in a given column, so if the LC value is very high, the spawn attempts will be very spread out across the column, and if the LC value is very low, the spawn attempts will be very concentrated within those blocks in the column. This is one reason why it is beneficial to build mobfarms lower in the world, as the spawns will be more concentrated within the farm, instead of spread out.
## Valid Spawning Locations
### Spawnable blocks
monsters can spawn on blocks with solid top surface
### Spawning inside other entities
up to 1.8 monsters can't spawn inside other entities. then up to 1.14 [someone confirm this] they can again. currently they can't again. [please confirm]
## Pack Spawning
### Pack Size
most monsters have a maximum pack size of 4
### Pack Spawn Attempt Jumping
spawn attempts that begin in one location, may jump to other locations via the pack spawning mechanic, even if the original location was not valid. this is why u dont want to have conductive blocks near the foot level of spawn pads in mobfarms (i think)
### Mob Caps:TODO link