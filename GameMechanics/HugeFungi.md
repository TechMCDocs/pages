---
title: Huge fungi
description: Mechanics of huge fungi generation and farms
---

# Huge fungi

Report conducted by ncolyer, with information gathered through code reading, multiple experiments, consultations, discussions, trial and error, and more
## Overview
This page will split into the 3 main ways to harvest huge fungi (more commonly referred to as 'Nether Trees', or 'Wart Trees')
- Manual Harvesting
- Semi-Automatic
- Self-Sustaining

Some theorised methods will also be discussed, such as RNG manipulation, and self-sustaining N-Core designs.
### Glossary:
- Rates:
 The farm's collectable item output, normally measured in items per hour
- Efficiency:
 The output compared to the maximum possible output, in this case, the number of blocks of a huge fungi that are harvested, compared to the number that can possibly generate.
- gt:
 Game ticks, 1/20th of a second
- RNG: 
Random Number Generator, can be manipulated using the seed.
- s: 
Used in all cases instead of alternative 'z' spelling.
### Growth Mechanics
1. Natural Generation
Huge fungi generate naturally in only 2 biomes situated in the Nether (DIM-1); the warped forest, where you can find warped huge fungi, and the crimson forest, where you can find crimson huge fungi. Huge fungi only generate naturally in their respective biome. 
To determine the size of huge fungi, a random integer is chosen between 4 and 13 [inclusive]. There is then a 1/12 chance of that integer doubling. The resulting value is the height of the trunk. A layer of wart blocks and shroomlights, or leaves, is then spread around the trunk, being able to generate up to 3 blocks out from the trunk. There is a 3x3x2 hollow ring that surrounds the lowest block of a fungus tree where no blocks generate.
![fungi_gen](https://i.imgur.com/8dHv9g5.png)
Layers 1-3 the leaf diameter (measured as a square) is 3, then at layers 4-17 it's 5 blocks, at layer 18-25 it reverts back to 3 blocks wide, and at layers 26 and 27, it gets reduced again to just 1 block wide.
2. Player Grown
If a player bonemeals a fungus, and if that fungus is directly above its respective nylium block (e.g. warped fungus on warped nylium), there will be a 40% chance that it will grow. So if a player bonemeals a fungus 4 times, there will be an 87.04% chance it will grow, (99.998% chance if 20 attempts are made).
Note: A fungus plant (the 'sapling' of huge fungi), will not grow by itself through random ticks, unlike traditional saplings.
When a fungus grows, it doesn't check for blocks above or around it, this allows for pistons to be situated adjacent to the trunk, allowing for extremely fast cycle speeds.
Note: That a fungus will not grow if it is obscured by the height limit or if it is placed outside of the world.
3. Dispenser Grown
Similarly, if bonemeal is placed inside of a dispenser, and then that dispenser is pointing into a fungus plant on its respective nylium block, then there will also be a 40% chance of it growing per attempt. Dispensers can be used to automatically grow the fungus plant, and up to 5 can surround a fungus plant, allowing for fungi to grow at 5hz on average. 
Note: There is no diminish in return with bonemeal efficiency, and dispensers used.
### Block Properties
A table showing the various blocks that are heavily involved in a Huge Fungi Farm:
![fungi_table](https://i.imgur.com/rQrmRej.png)
### Harvesting Methods
There are a multitude of ways in which huge fungi can be farmed, here are some of the more common and technical methods.
#### Manual Harvesting
During the early game where redstone resources are limited, certain manual harvesting methods are utilised. While it is recommended that a player gathers resources to make a farm, rather than spending that time manually farming, certain manual harvesting methods have arisen.
#### Nether Exploration
Collecting huge fungi blocks in their own biomes is a great way to collect a sufficient amount of wood and decorative blocks early game. 
The fastest way to use this method is to make an efficiency 5 netherite axe and hoe, then travel to the nether and fly around using an elytra until you come across a warped or crimson forest. If it's a crimson forest, be sure to bring warped fungus to repel hoglins (must be planted, with an area of effect of 15 blocks). 
After planting a fungus, build a 3x3 platform of gold blocks with a beacon on top for the haste 1 effect. Fly up from the beacon base and destroy the netherrack obstructing the beacon beam. The haste 1 effect, will reduce the mining time per stem block by 2gt [check]. This will also help when mining wart blocks and shroomlights, as when combined with an efficiency 3 netherite or diamond hoe, you will be able to mine each block in 1gt [check]. Setting up and removing a 1 layer beacon in the nether takes on average 600gt, so this beacon method will only be effective if you are mining more than 300 stem blocks, or if it is necessary for instant mining more than 38 blocks. 
Once the beacon is set up, you can begin harvesting trees. Huge Fungi normally generate together in clumps, so it's best to climb to the top using twisting vines, and then mine from the top down. Be sure to use a hoe to mine the leaf blocks (wart blocks and shroomlights), and your axe to harvest the stems.
![fungi_harvesting](https://i.imgur.com/Vk8g6bi.png)
To marginally increase speed, carry with you a few shulker boxes of unbreaking 3, efficiency 5, gold axes to reduce the mining speed by 1gt per stem block. 
#### Greenhouse
Place 2 nylium in a 3x3 grid pattern with 6 blocks in between each one. Then construct a water stream platform beneath it. Have the water stream funnel the items into a chest storage, connected by a hopper. To use the manual farm, place down a fungus on each nylium block, then grow the fungi using bonemeal. Now climb to the top using ladders, a bubble column, scaffolding or vines, and mine out the huge fungi from the top down. After harvesting the huge fungi, collect any items that didn't drop into the water stream below. 
![fungi_greenhouse](https://i.imgur.com/wUlpwwM.png)
#### Block Chamber/Storage
Build a traditional semi-automatic huge fungi farm, and connect the block stream to a block chamber. When you would like to harvest the blocks, climb to the top of the storage and mine them out using haste 2 and an efficiency 5 netherite axe and hoe.
https://www.youtube.com/watch?v=jytLCfRu_54
#### Semi-Automatic
Semi-Automatic harvesting is when everything in a farm is self-sufficient except for the input. 
In the case of semi-automatic huge fungi farms, this would be the placement of fungi plants and often, but not always, supplying it with bonemeal.
- Positives:
 Very fast rates, inexpensive, lag friendly on small scales, produces every block from the tree (wart blocks, shroomlights and weeping vines included)
- Negatives: 
Usually quite block inefficient, consumes high amounts of bonemeal, high-frequency designs can be locational and directional
1. Single Core
1 nylium, slow rates, average efficiency.
https://www.youtube.com/watch?v=yY64bj2DjhA
https://www.youtube.com/watch?v=nlfAwEV-cCk
2. Dual Core
2 nylium, average rates, low efficiency.
https://www.youtube.com/watch?v=XAIsjTTGpPA
 (outdated)
https://www.youtube.com/watch?v=yY64bj2DjhA
3. Quad-Core
4 nylium, fast rates, low efficiency.
https://www.youtube.com/watch?v=rhncko7vKLs
4. N-Core
A farm consisting of N-modules connected by a sufficient transportation system. Extremely fast rates, maxing out at 5,400,000/h with an auto-clicker (20hz) and around 1,400,000/h at vanilla placement speed (5hz), high efficiency.
https://www.youtube.com/watch?v=rhncko7vKLs
https://youtu.be/nMkffHVLHvw
 (ongoing)
#### Self-Sustaining
Once built, a Self-Sustaining Huge Fungi Farm can run without player interaction as long as it is kept loaded.
- Positives: 
Can run in loaded chunks without the need for a player to plant any fungus, can generate a surplus of bonemeal
- Negatives:
 Slow, more expensive to build, laggy, and limited (if any) wart blocks and shroomlights are produced, often unreliable.
 
1. Growing Area
Max 5x5 area of nylium to grow fungus plants using bonemeal.
Flying Machine
Flying Machine pushing into a TNT dropping zone.

https://www.youtube.com/watch?v=dEToZ4wLuqY

2. Blast Chamber
Directly Above, Piston wall pushing to external

https://www.youtube.com/watch?v=M51sTIwDo5w

3. N-Core
Theoretically, the fastest way to harvest huge fungi 
#### RNG Huge Fungus Farming
Manipulate RNG to generate a 5x5 platform of fungus, which all grow on the first bonemeal attempt to produce the largest huge fungi possible (7x7x27)
#### Fungus Farming
The collection of fungus plants to grow in a huge fungi farm

Dissection of nether foliage distribution:

https://www.youtube.com/watch?v=SOcY9En4l_E