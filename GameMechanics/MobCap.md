---
title: Mob caps
description: Description of the mob caps
---

# mob caps

## Description of the Mob Cap
The mob cap is an essential part of how Minecraft's mob spawning algorithm works. The mob cap serves as a limit on how many mobs can exist at once within your world. Each category of mobs has its own mobcap, which is calculated by the chunks that are currently being loaded in any given dimension.
Categories:
- Hostile Mob Cap (Constant - 70)
- Passive Mob Cap (Constant - 10)
- Ambient Mob Cap (Constant - 15)
- Water Creature Mob Cap (Constant - 5)
- Water Ambient Mob Cap (Constant - 20)

The hostile mob encompasses mobs that are hostile towards the player (monsters). The passive mob cap is composed of mobs that do not attack the player even if provoked (there are certain exceptions such as llamas or wolves). The ambient mob cap is composed only of bats. The water creature mob cap is composed of squid and dolphins and the water ambient cap is composed of all other fish.
## Calculating the Mob Cap:
The mob cap can be calculated using the following formula:
mobCap = constant + (chunksInRange / 289)
Due to the number of chunks in range on single player being fixed to 289 (regardless of view distance), the caps on single player worlds are always fixed at the constant values above. On multiplayer servers, the chunks in range increase as the players spread out further. By default, players in spectator mode contribute to the mob cap (since 1.13), however, this can be toggled using the `spectatorsGenerateChunks` gamerule.

![mobspawningrange](https://cdn.discordapp.com/attachments/804010832467984415/817074344781545472/801.png)
## Mob Cap mechanics
Each dimension has its own mob cap that is calculated using the method described above. Every tick, a check is made on the mobcap. If the current list of entities per category matches or exceeds the current cap the spawning attempts are stopped for that category. New monsters will not spawn outside of the 128m radius around the player (since 1.13), but any mobs that end up outside of that will despawn instantly. In 1.12 monsters would still spawn in 15x15 chunks around the player, but they would despawn after 1 gametick of being alive.
### Exceptions
The following hostile mobs count towards the mob cap but do not despawn and as such can be used in mob switches:
- Withers
- Elder Guardians (not in 1.15.2+)
- Shulkers
- Villagers that have been converted to zombies after trading with them
 (1.15+)
 
 Some mobs don't respect the mob cap rules, such as:
- Phantoms
- Raids
- Patrols
- Spawner (has its own rules)
- Mobs spawned by lightning strikes (Pig->Pigman (Zombiefied Piglin) ,Villager->Witch, Skeleton horse)
- Portal Pigman (Zombiefied Piglin)
- Endermite
- Vex
- Wandering trader
- Additionally, mobs that have been nametagged or have picked up an item (or riding another entity in 1.16+) will not count towards their respective mob cap.
