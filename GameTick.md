---
title: Game Tick
description: All the information about the tick phases
---
{% include subpages.html %}

# Game Tick

A gametick (commonly abbreviated "gt", or simply "tick") is a part of the game loop where the game logic is processed.
Many different things are done in a tick, they are grouped into what we call tick phases.

These are the ones commonly used for technical Minecraft:

1. Block/fluid tile ticks
2. chunkManager tick
3. blockevent processing
4. regular entities ticking
5. block entities ticking
6. player actions and other sheduled tasks

Here is a detailed breakdown of the server tick:

# `runServer()`
`net.minecraft.server.MinecraftServer.runServer()`  
This is the main server loop.

The main loop:
1. Initialize the loop
2. If the server is running, check if the server can keep up (<=50mspt), and if it can't (>50mspt) log a warning.
3. Create a new tick monitor
4. Start the tick
5. Call `net.minecraft.server.MinecraftServer.tick()`
6. Wait for the next tick
7. End the tick and stop the tick monitor

If an exception occurs this loop will shut down the server and try to create and save a crash report.

# `MinecraftServer.tick()`
`net.minecraft.server.MinecraftServer.tick()`  
This method is called in `runServer()`

## Basic tick initialization:
- Increase the number of ticks by one
- Get the time of the start of the tick
- Call `net.minecraft.server.MinecraftServer.tickWorlds()`
- Player managing
- Autosave if the tick number is divisible by 6000
- Update snooper if tick number is divisible by 6000
- Calculate length of tick in milliseconds

## `MinecraftServer.tickWorlds()`
`net.minecraft.server.MinecraftServer.tickWorlds`  
This method is called in `MinecraftServer.tick()`

- Call the command function manager
- Sync world time with other dimensions every 20gt
- Call `net.minecraft.server.ServerWorld.tick()`, which is the main in-game loop
- Tick network IO
- Update player latency
- Refresh server GUI

# `ServerWorld.tick()`
`net.minecraft.server.ServerWorld.tick`  
This method is called in `MinecraftServer.tickWorlds()`

The following actions/processes are performed during every tick:

## Tick World Border

## Weather:
- Rain
- Thunder
- Player sleeping

## Block Ticks
- Cleaning out the tick cache to differentiate between old ticks and ticks that still need to happen
- Schedule a block tick if it's valid

## Fluid Ticks
- Cleaning out the tick cache to differentiate between old ticks and ticks that still need to happen
- Schedule a fluid tick if it's valid

## Raids
- Check if ongoing
- Do raid stuff

## Chunks
- Purge unloaded chunks
- TACS stuff (?)
- Tick chunks
  - Update chunk load levels
  - Natural mob spawning
  - Mob spawning by ticking spawners
  - Random ticks
    - Spawn lightning bolts & skeleton horse traps from thunder
    - Form snow & ice
    - Randomtick 3 blocks per subchunk
  - Player movement
- Unload chunks (ticks TACS)
  - Write POIs
  - Unload chunks
  - Initialize chunk cache

## Block Events
- Call `synchedBlockEvent` hashmap and execute the block events stored in it

## Entities
- If there is an ongoing dragon fight tick the dragon
- Check for despawn
- Tick entities
- Remove entities from the world
- Actually populate the spawns in the world / load them into the world (different to the spawning in the chunk ticks)

## Block Entities

## Player Inputs??

# World Border
`net.minecraft.world.border.WorldBorder.tick`  
Called in the game loop.

The only thing that will happen during this "ticking" of the world border is checking if the world border needs to change in size, and executing that.

# [Weather Cycle](https://techmcdocs.github.io/pages/GameTick/Weather/)
See separate article.

# [Block & Fluid Ticks (Tile Tick Phase)](https://techmcdocs.github.io/pages/GameTick/BlockTicks/)
See separate article.

# Handle raids
TODO

# Chunk Manager Tick
(just before block events since 1.17.1, was before tileticks)

- Purge unloaded chunks
- TACS stuff
- Chunk ticks
  - Handle chunk loading and unloading tickets
  - Send chunk packets like block changes 
  - Do natural mob spawning
  - Mob spawning by ticking spawners
  - Random events
    - Handle lightning, ice and snow
    - Do random ticks
  - Player movement
- Unloading
  - Tick POI’s
  - Unload chunks
  - Initialize chunk cache

# Block Event Phase
Block events are processed in this phase. This includes things such as:

- Piston extension/retraction
- Noteblock play note
- Chest/endercher/shulker open/close (change open count)
- Bell updating villagers when hit
- Gateway start cooldown
- Mobspawner reset delay

Block events are stored in a LinkedHashSet. The game loops through block events and processes them. Piston block events can activate and schedule another block event that is added a the end of the LinkedHashSet.This allow chaining multiple blockevents in the same tick.

Blockevents are sent directly to the client the execute them also clientside.

## Order of processing:
In the block event phase, the game calls `processSyncedBlockEvents()` from `net.minecraft.server.ServerWorld`. All block events are stored in the `synchedBlockEventQueue`. This queue is an `ObjectLinkedOpenHashSet`. This is a type specific linked hash set which uses a hash table to represent a set. For more information about hashsets see [Earthcomputer's Video](https://youtu.be/y5Cx07OHaOI). This is a linked hash set which means a list is stored as well as a hash set. This list is used for iteration.

```java
public int hashCode() {
int i = this.pos.hashCode();
i = 31 * i + this.block.hashCode();
i = 31 * i + this.type;
i = 31 * i + this.data;
return i;
}
```

`this.pos.hashCode()`:
```java
public int hashCode() {
return (this.getY() + this.getZ() * 31) * 31 + this.getX();
}
```

`this.block.hashCode()`:
```java
public int hashCode() {
int i = this.self.hashCode();
i = 31 * i + this.other.hashCode();
i = 31 * i + this.facing.hashCode();
return i;
}
```

`this.self.hashCode()`, `this.other.hashCode()` and `this.facing.hashCode()`:  
Uses the standard `hashCode()` method from the `Object` class in java. `self` and `other` are `net.minecraft.block.BlockState` instances. `facing` is a `net.minecraft.util.math.Direction` instance.

This means that a block event's hashcode depends on:  
- Its position (x, y, z)
- The type of block. All different attributes of the block are taken into account. This means that this part should be the same for all blocks with the same attributes and thus should be the same for 2 exact copies of a block in different positions.
- The type of block event (see next section)
- The data of the block event (see next section for how data is determined)

`processSynchedBlockEvents` will go through the `syncedBlockEventQueue` until it’s empty. First there will be a check. Here the game will get the `BlockState` of the block in the position where the `BlockEvent` is called. If this `BlockState` corresponds to the block state of the `BlockEvent` then processing will continue. If not the code will go to the next object in the queue. This can happen when for example the block is broken or has changed state already.

After this the `onSynchedBlockEvents` method will be called. After this there are 2 possibilities. It’s either a block entity or a normal block. There is a difference between those two in what happens. More about this will be discussed later

## Types, data and specifics (`addSynchedBlockEvent()`):
The order in which block events happen is both locational and directional since it is stored in a hashset.  
Things that call `net.minecraft.server.world.serverworld.addSyncedBlockEvent`:

| Block Name        | Type | Data                                         | Called When                     |
|-------------------|------|----------------------------------------------|---------------------------------|
| Bell Block        | 1    | `direction.getId()` (side bell was hit from) | hit                             |
| Noteblock         | 0    | 0                                            | played                          |
| Extending Piston  | 0    | direction piston is facing                   |                                 |
| Retracting Piston | 1    | direction piston is facing                   |                                 |
| Moving Piston     | 2    | direction piston is facing                   |                                 |
| Chest             | 1    | amount of players viewing inventory          | opening or closing              |
| Shulker Box       | 1    | amount of players viewing inventory          | opening or closing              |
| Ender Chest       | 1    | amount of players viewing inventory          | opening or closing & every 20gt |
| End Gateway Block | 1    | 0                                            | after teleporting entity        |
| Mob Spawner       | 1    | 0                                            | updating spawns                 |

## What a block event does (`onSynchedBlockEvent()`):
So now we know when a block event gets scheduled, how its hash works, what actions schedule block events etc. But we don’t yet know what it does, why block events are used etc.

First the `onSynchedBlockEvents()` method from `AbstractBlock` gets called. This will then call a different method depending on if the block is a normal block or a block entity.

### Block Entities:
Block entities is the biggest category. Here the `onSynchedBlockEvents()` method from `BlockWithEntity` gets called. First will `super()` the method from `AbstractBlock`. This is a deprecated method and will always return false, thus won’t do anything. After this it will check if the block entity isn’t null. Now `blockEntity.onSyncedBlockEvent(type, data);` gets called. This will first call the block specific method and then `super()` some methods.

### Bell Block:
The bell block will ignore the block event if the type isn’t 1 (return `false`). If the type is 1 the following happens:
- bell memories get notified. This mostly does some variable allocations
- amount of ticks the bell is resonating is set to 0.
- side on which the bell got hit is recorded.
- ringing time gets set to 0 and ringing is set to true.
- `true` gets returned.

### Chest Block:
If the type is 1, it will set `viewerCount = data` and return `true`. Otherwise it will return `false`.

### End Gateway Block:
If the type is 1, it will set the `teleportCooldown = 40` and return `true`. Else it will return `false`.

### Ender Chest Block:
If the type is 1, it will set `viewerCount = data` and return `true`. Else it will return `false`.

### Mob Spawner Block:
If the type is 1 and the call is client-side, `spawn delay = min spawn delay` and `true` is returned. Else `false` is returned.

### Shulker Box:
if type isn’t 1, return `false`.
If data = 0 close the shulker box and return `true`
if data = 1 open the shulker box and return `true`

### Blocks:
For normal blocks it’s a little simpler. Here the block specific method gets called immediately. After this `super()` methods get called.

### Comparator Block:
All the code about block events in comparator block classes doesn’t do anything. It is most likely leftover or something. There’s also no possible way to have a block event on the position of a comparator execute...

### Note Block:
Play the note, display the particles and make the sound in game, then return `true`.

### Piston Block:
TODO

# Entity Phase

## Handle ender dragon fight

## Regular entities
- Entities move
- Entities trigger tripwire hooks
- Entities trigger pressure plates.

### Order of processing:
Hierarchy:
1. `net.minecraft.server.world.ServerWorld.tickEntity()`
2. Entity-specific class with a `tick()` method
   1. `net.minecraft.entity.mob.MobEntity.tick()` is called via `super()`
   2. `net.minecraft.entity.LivingEntity.tick()` is called via `super()`
   3. `net.minecraft.entity.Entity.tick()` is called via `super()`
      1. `net.minecraft.entity.mob.MobEntity.basetick()` gets called
      2. `net.minecraft.entity.LivingEntity.basetick()` is called via `super()`
      3. `net.minecraft.entity.Entity.basetick()` is called via `super()`

…  
More detail on this will come in the next sections:

### `ServerWorld.tickEntity()`
First the game looks if the entity needs to be ticked. It doesn’t tick the entity if either the entity is an object of the player class or if `getChunkManager().shouldTickEntity(entity)` returns `false`.  
If either of these conditions aren’t satisfied then `this.checkEntityChunkPos(entity)` is called.

If the entity needs to be ticked the following happens:
1. the `position`, `pitch`, `yaw`, etc. variables get reassigned from `entity.prevYaw` to `entity.yaw`
2. if the entity needs to be updated, its `age` gets advanced by 1
3. the entity will be registered in the profiler and the entity will be ticked
4. the entity's chunk position will be checked
5. if there are any passengers they will be ticked too

In the following sections we’ll be looking at everything that happens in a **chronological order**

### `SpecificMob.tickEntity()`
Most mobs override the `tick()` method from `MobEntity.java`. In this method the method from `MobEntity` gets supered.  
We’ll look at specific mobs after we’ve described the general way in which mobs work.

### `MobEntity.tick()`
This overrides the `tick()` method from `LivingEntity`.
1. the `tick()` method from `LivingEntity` gets supered
2. it checks if the instance is client or server & if it is server it will update the leash if the mob has one
3. it will also update controls every 5 ticks, basically enabling or disabling whether the entity can take control of the mob

### `LivingEntity.tick()`
This overrides the `tick()` method from `Entity`.  
A lot of this stuff doesn’t apply to mobs, only to players

1. the `tick()` method from `Entity` gets supered
2. `tickActiveItemStack()` is called (this basically updates the item someone is holding)
3. updates the leaning pitch (swimming or not)
4. the following only happens if the world is an instance of server:
   1. update despawn timers of stuck arrows
   2. update despawn timers of stuck stingers
   3. something with armour slots
   4. update the damage tracker
   5. update if the mob should be glowing
   6. update sleeping (waking up or not)
5. call `tickMovement()` (see later)
6. hand animation and position
7. yaw and bobbing
8. head turning
9. range checks (basically makes sure tilts and angles are between -180° and 180°)

### `Entity.tick()`
1. sets a flag if the entity is glowing
2. the `baseTick()` method gets called.

### `MobEntity.baseTick()`
This overrides the baseTick method from LivingEntity.
1. `super()` method gets called
2. does sound stuff

### `LivingEntity.baseTick()`
This overrides the baseTick method from Entity
1. update hand swinging variables (old -> new)
2. sets bed position if needed
3. displays soul speed effect if needed
4. `super()` method gets called
5. damage the entity if it’s inside a wall
6. damage an entity if it’s outside of the world border
7. extinguish fire if needed
8. if the entity is under water it updates the air bubbles and damages it if needed
9. kicks the entity from a vehicle if it can’t be ridden on water
10. updating of hurt timer and regen timer
11. checks if the entity has died
12. status effects
13. updating of movement and looking variables (old - > new)

### `Entity.baseTick()`
This is the upper most `baseTick()` method
1. stop riding vehicles if needed
2. change riding cooldown
3. update some movement variables
4. tick nether portals
5. spawn particles
6. update water stuff: swimming etc
7. extinguish fire
8. set on fire if in lava
9. kill the entity if it’s y value is lower than -64
10. set a flag for the amount of ticks it needs to stay on fire

### `tickMovement()`
This is called within `LivingEntity.tick()`  
If it’s a passive animal the first method that will be called is the method from `net.minecraft.entity.passive.PassiveEntity`  
If it’s not there’s a mob specific method. In the next sections i will go over all the `tickMovement()` methods from `PassiveEntity.tickMovement()` all the way up to `LivingEntity.tickMovement()`. For non passive entities the specific methods will be adressed in the sections about that mob.

### `PassiveEntity.tickMovement()`
This overrides the `tick()` method from `MobEntity`.
1. call the `super()` method
2. the following only happens client side:
   1. the following happens only if happyTicks is greater than 0 (happyTicks is set to 40 when a baby grows up and is used for particles)
      1. if happyTicks is a multiple of 4 emit particles
      2. remove 1 from the happyTicks value
3. the following only happens on the server side:
   1. sets the breeding age

### `MobEntity.tickMovement()`
This overrides the `tick()` method from `LivingEntity`.
1. super the method from `LivingEntity`
2. this happens only on the server side:
   - if the mob can pick up loot and
   - if the mob is alive and
   - if the mob is not dead and
   - if gamerule mob griefing is true the mob will try to pick up stuff from the ground

### `LivingEntity.tickMovement()`
Most of the movement stuff happens in this method.
1. decrease `jumpingCooldown`
2. body tracking
3. change yaw, pitch, body elements, head etc
4. change velocity
5. If the mob is immobile set all speeds to 0
6. if the mob can move voluntarily call `tickNewAI()` (see next section)
7. jumping:
   1. look at the height of fluids
   2. swimming upwards
   3. jumping
8. travel with the different speeds set in `tickNewAI()`
9. pushing:
   1. decrease `riptideTicks` and tick riptide
   2. do cramming
10. drowning

### `MobEntity.tickNewAI()`
this is being called in `LivingEntity.tickMovement()` and handles all of the AI stuff. This is separated into sections based on the profiler loggings.
1. add to the despawn counter
2. sensing
3. targetSelector
4. goalSelector
5. navigation
6. mob tick
7. controls
8. move
9. look
10. jump

#### Sensing
1. clear the visibility cache (contains visible and invisible entities)

#### `targetSelector`
Here `targetSelector` gets ticked, which is a `GoalSelector` object.  
What happens in the `GoalSelector.tick()` method:
1. remove unnecessary goals
2. update goals
3. tick each goal

#### `goalSelector`
Call `this.goalSelector` which is an `GoalSelector` object.  
What happens in the `GoalSelector.tick()` method:
1. remove unnecessary goals
2. update goals
3. tick each goal

#### Navigation
Call `this.navigation` which is an `EntityNavigation` object.
1. recalculate the path if needed
2. return if the mob is idle
3. if the mob is at a valid position continue following the path
4. if the path is finished go to the next one

#### Mob Tick
This method is different for each mob and will be addressed for each mob specifically.

#### Move
Calls `MoveControl.tick()`.

#### Look
looking

#### Jump
jumping

# Tile Entity Phase
Tile/Block entities are processed in this phase. This includes things such as:
- Moving blocks turn into normal blocks
- Moving blocks push entities and slime give velocity
- Furnaces check for items in their inventory
- Hoppers push and pull items
- Sculk sensors activate.

# Player Inputs Phase
Player inputs are processed in this phase. This includes things such as:
- Flipping levers
- Pushing buttons
- Placing blocks
- Breaking blocks

# Other Things Calculated In A tick
There are other things that are calculated in a ticks that isnt specific to a phase, for example rails and redstone dust is calculated recursivly independent from the ticks and can happen in all of them. These components are called "recursive updators" or "instant updators".
