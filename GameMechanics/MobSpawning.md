---
title: Spawning algorithm
description: Description of the spawning algorithm
---

# Spawning Algorithm

## LC Value
LC value represents the height up to which the spawning algorithm distributes spawns within a 1x1 column of blocks in the world. The LC value is determined by the y level of the highest non-air block in the column (In 1.12.x and below it was the y level of the highest skylight-obstructing block, ceilinged mod 32, minus one).
The spawning algorithm gets a set number of spawn attempts to distribute amongst all the blocks in a given column, so if the LC value is very high, the spawn attempts will be very spread out across the column, and if the LC value is very low, the spawn attempts will be very concentrated within those blocks in the column. This is one reason why it is beneficial to build mobfarms lower in the world, as the spawns will be more concentrated within the farm, instead of spread out.

## Valid Spawning Locations

### Spawnable blocks
Mobs can spawn on most blocks with a solid top surface.  
There are however a few exceptions where some blocks are non-spawnable, despite fitting this requirement. They are listed as follows:
- Barrier blocks
- Bedrock
- Beacons
- Glass (including stained- and tinted glass)
- Trapdoors

There are also a few blocks that restrict spawning to only a few select mobs:
- Polar bears on ice and frosted ice
- Fire-immune mobs on magma blocks
- Ocelots and parrots on leaves
- Wither skeletons inside wither roses

Soul sand is also spawnable, despite not having a full top surface.

### Spawning inside other entities
<mc-version-block range="<1.9||>=1.13">
Monsters can't spawn inside other entities.
</mc-version-block>
<mc-version-block range=">=1.9&&<1.13">
Monsters can spawn inside other entities.
</mc-version-block>

## Pack Spawning

### Pack Size
Most monsters have a maximum pack size of 4.

### Pack Spawn Attempt Jumping
Spawn attempts that begin in one location, may jump to other locations via the pack spawning mechanic, even if the original location was not valid. This is why you dont want to have conductive blocks near the foot level of spawn pads in mobfarms.

## Mob Cap
Main article: [Mob Caps](https://techmcdocs.github.io/pages/GameMechanics/MobCap/)

## Mob Spawning Algorithm in Detail

### `ServerChunkManager.tickChunks()`

Mob spawning happens early in the tick inside `ServerChunkManager.tickChunks()`.

The important things for mob spawning that happens are:  
`boolean timeModulus = levelProperties.getTime() % 400L == 0L;`  
This `timeModulus` will only be `true` when the world time is divisible by 400. This is used for the passive mob spawning cycle.

Now there is a loop going over all loaded chunks.
In this loop, for each chunk the game checks if there’s a non-spectator player inside a 128 radius of the chunk. If this is not true, mob spawning won’t even happen in this chunk. The method will be terminated with a return.

If this is the case, the local difficulty will increase.  
After this mobs will spawn if the gamerule `doMobSpawning` is `true` and if the chunks are within the world border. `SpawnHelper.spawn()` is called.

### `SpawnHelper.spawn()`
In this method, the game will iterate over all spawn groups. The different spawn groups can be found in `SpawnGroup.java` and can be found in the next section.
It checks for a few conditions before proceeding with mobspawning. Spawning will terminate if either of the following conditions is true.
- The spawn group is animal (`CREATURE`) and the `timeModulus` is `false` (time not divisable by 400)
- The number of loaded mobs exceeds the mobcap

After these checks, `SpawnHelper.SpawnEntitiesInChunks()` is called.

### Spawn Groups:

| SpawnGroup     | name           | spawnCap | peaceful | isAnimal | despawnRange |
|----------------|----------------|----------|----------|----------|--------------|
| MONSTER        | monster        | 70       | false    | false    | 128          |
| CREATURE       | creature       | 10       | true     | true     | 128          |
| AMBIENT        | ambient        | 15       | true     | false    | 128          |
| WATER_CREATURE | water_creature | 5        | true     | false    | 128          |
| WATER_AMBIENT  | water_ambient  | 20       | true     | false    | 64           |
| MISC           | misc           | -1       | true     | true     | 128          |

The first value is the name of the group.  
The second value is the mobcap.  
The third value indicates whether the mob can spawn in peacful mode.  
The fourth value indicates if the mob is an animal.  
The fifth value indicates the size of the despawn sphere.

Name decoding:
- `monster`: Hostile mobs
- `creature`: Animals
- `ambient`: Bats
- `water_creature`: Squid, dolphins etc.
- `water_ambient`: Fish
- `misc`: A special group only used for mob spawning

There are some things to notice in here:
- Every spawn group has a despawn sphere of size 128 except for fish
- The `misc` group doesn’t have a mobcap (it doesn’t get used for natural spawning)

### `SpawnHelper.spawnEntitiesInChunks()`
There are multiple methods with the same name. They are called in an order and I will go over all of them in the same section in the order they get called.

First an initial spawn position is chosen inside the chunk. This happens in the following manner:
- X position: random position within the chunk
- Z position: random position within the chunk
- Y position: The Y position is decided by the heightmap (see section on heightmap)

Now we have chosen a random position to spawn the mob inside the chunks. If this position is lower than y = -63 then the spawn attempt will be terminated.

### Heightmap with regards to mob spawning (formerly known as LC value)
When the heightmap generates it goes over each block in the chunk. It searches for the highest subchunk with blocks inside. When it has found this, the game will start from the top of the subchunk and work it’s way down, until it finds a block that isn’t air. When it has found this block, the heightmap for this xz position will be the y position of the highest block + 1.

When the y position for mob spawning is decided the following happens:  
A random number on interval `[-64, heightmap + 1] = [-64, highest_block + 2]` inclusive, gets chosen. This position is then passed as the y position for the spawn attempt.

The probability of a certain position being chosen is:  
`P(spawn) = 1 / (highest_block + 66) = 1 / (highest_possible_spawn_position + 65)`

Examples (y is the highest block in the 1x1 block column):  
y = -64:` P(spawn) = 1 / (0 + 2) = 1 / 2 = 0.5`  
y = -62: `P(spawn) = 1 / (2 + 2) = 1 / 4 = 0.25`  
y = -4: `P(spawn) = 1 / (60 + 2) = 1 / 62 = 0.0161`  
y = 136: `P(spawn) = 1 / (200 + 2) = 1 / 202 = 0.00495`

The probability of a mob spawning increases rapidly the higher you get.

