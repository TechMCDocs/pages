---
title: Game Tick
description: All the information about the tick phases
---
{% include subpages.html %}

# tick

A tick is a part of the game loop where the game logic is processed.
Many different things are done in a tick, and some of them a grouped in "tick phases."
The ones commonly used for redstone are theses:

1. Block/fluid tile ticks
2. chunkManager tick
3. blockevent processing
4. regular entities ticking
5. block entities ticking
6. player actions and other sheduled tasks

Here is a detailed breakdown of the server tick
## runServer()
`net.minecraft.server.MinecraftServer.runServer`
This is the main server loop.

The main loop:
- Initialize the loop
- If the server is running, check if the server can keep up (<=50mspt), and if it can't (>50mspt) log a warning.
- Create a new tick monitor
- Start the tick
- Call `net.minecraft.server.MinecraftServer.tick`Wait for the next tick
- End the tick and stop the tick monitor

If an exception occurs this loop will shut down the server and try to create and save a crash report.
## MinecraftServer.tick()
`net.minecraft.server.MinecraftServer.tick`
This method is called in `runServer()`
### Basic tick initialization:
Increase the number of ticks by one
Get the time of the start of the tick
Call `net.minecraft.server.MinecraftServer.tickWorlds`Player managing
Autosave if the tick number is divisible by 6000
Update snooper if tick number is divisible by 6000
Calculate length of tick in milliseconds
### tickWorlds()
`net.minecraft.server.MinecraftServer.tickWorlds`
This method is called in `MinecraftServer.tick()`
- Call the command function manager
- Sync world time with other dimensions every 20 ticks (1 second)
- Call `net.minecraft.server.ServerWorld.tick`, which is the main in-game loop
- Tick networkIO
- Update player latency
- Refresh server GUI

### ServerWorld.tick()
`net.minecraft.server.ServerWorld.tick`
This method is called in `MinecraftServer.tickWorlds()`
#### Tick world Border
#### Weather:
- - Rain
- - Thunder
#### Player sleeping
#### Chunks:
- Purge unloaded chunks
- TACS stuff (?)
- Update chunk load levels
- Natural mob spawning
- Mob spawning by ticking spawners
- Random ticks
- Player movement
- Write POIs
- Unload chunks
- Initialize chunk cache
- World Border`net.minecraft.world.border.WorldBorder.tick` is called in the game loop. The only thing that will happen during this "ticking" of the world border is checking if the world border needs to change in size, and executing that.
- Weather Cycle: TODO:move to a separate article
In the weather section of the tick function there are 6 variable allocations.
The first one is a variable you get from a function that decides if it’s raining or not from rain gradient (the sky colour, but more later on this). The other variables are clearWeatherTime, rainTime, thunderTime, isRaining and isThundering from the world properties.
The first part of the code is an if block that checks if `clearWeatherTime` is bigger than 0. This part is basically a hackfix to make it so that the weather will stay clear for a certain amount of time when the `/weather clear [time]` command is used. In this block `clearWeatherTime` will count down. After this `thunderTime` and `rainTime` will be set to 1 if it’s not thundering or raining respectively. If it is thundering or raining they will be set to 0. After this `thundering` and `raining` will be set to false in the properties.
What this accomplishes will be that once `clearWeatherTime` has reached 0, it will start raining and thundering the next tick.
If `clearWeatherTime` isn’t bigger than 0, `thunderTime` and `rainTime` will be checked.
If `thunderTime` is bigger than 0 we’ll check if `thunderTime - 1` is equal to 0. If that is the case, then `thundering` will be reversed. This means if it was thundering, it will stop thundering and if it wasn’t thundering it will start thundering.
If however `thunderTime` isn’t bigger than 0, a new value is set for thunder time. This value is decided by:
thunderTime = thundering ? this.random.nextInt(12000) + 3600 : this.random.nextInt(168000) + 12000;
In a more readable format, this will mean that:
If it is thundering, the new `thunderTime`  will be between 3 600 and 15 600.
If it is not thundering, the new `thunderTime`  will be between 12 000 and 180 000.
Which value somewhere in this range it becomes is determined by a random factor.
The same process happens for `rainTime`, though the new value for `rainTime` is determined differently:
rainTime = propertiesRaining ? this.random.nextInt(12000) + 12000 : this.random.nextInt(168000) + 12000;
In a more readable format, this will mean that:
If it is thundering, the new `rainTime` will be between 12 000 and 24 000.
If it is not thundering, the new `rainTime` will be between 12 000 and 180 000.
Which value somewhere in this range it becomes is determined by a random factor.
After this the values that were changed will be reallocated to the corresponding fields.
So now for the explanation of how this actually works. The way in which this works is a little confusing 
`clearWeatherTime` isn’t used to determine how long the weather will stay clear outside of commands. Instead what happens is:
The game starts and `clearWeatherTime`, `rainTime` and `thunderTime` are set to 0. `raining` and `thundering`  are set to false. Now, since everything is set to 0 it will immediately go to the part where it sets the timers, using the formulas we’ve just seen. In this case `rainTime` and `thunderTime` will be used to indicate how long it will **not** be raining or thundering. Both timers will count down, and only when they have reached 0 it will start raining or thundering, depending on which timer ran out. It will start this weather by flipping the boolean as we’ve seen before. After this a new time will be set for `rainTime` or `thunderTime`. This time, they’ll be used to indicate how long it will be raining or thundering. By constantly flipping the time `rainTime` and `thunderTime` represent, there’s no need to use `clearWeatherTime`. Thus we only have a value for this if we want to force the game to keep the weather clear for a certain amount of time no matter what.
#### Tile tick phase
Tile ticks are processed in this phase. The game select tile ticks which have their processing tick (current tick when scheduled+ delay)  smaller or equal to the current tick.

maximum 65536 tile ticks can be processed per tick,if there is more to process,the last tile tick will be delayed to the next tick
Block tileticks an fluid tileticks are proecessed one after the other,with each their own tile tick cap.



#### Handle raids

Chunk Manager tick
(just before block events since 1.17.1, was before tileticks)

-Handle chunk loading and unloading tickets
-send chunk packets like block changes 
-Do natural mob spawning
-Mob spawning by ticking spawners
-Handle lightning, ice and snow
-Do random ticks
-Player movement
-Tick POI’s
-Unload chunks
-Initialize chunk cache



#### Block event phase
Block events are processed in this phase. This includes things such as:

- piston extension/retraction
- noteblock play note
- chest/endercher/shulker open/close (change open count)
- bell updating villagers when hit
- gateway start cooldown
- mobspawner reset delay

Block events are stored in a LinkedHashSet. The game loops through block events and processes them. Piston block events can activate and schedule another block event that is added a the end of the LinkedHashSet.This allow chaining multiple blockevents in the same tick.

Blockevents are sent directly to the client the execute them also clientside.

#### Handle ender dragon fight

#### Regular entity phase
Regular entities are processed in this phase. This includes things such as:
- entities move
- entities trigger tripwire hooks
- entities trigger pressure plates.
#### Tile entity phase
Tile/Block entities are processed in this phase. This includes things such as:
- moving blocks turn into normal blocks
- moving blocks push entities and slime give velocity
- furnaces check for items in their inventory
- hoppers push and pull items
- sculk sensors activate.
#### Player inputs phase
Player inputs are processed in this phase. This includes things such as:
- flipping levers
- pushing buttons
- placing blocks
- breaking blocks

## Other Things Calculated In A tick
There are other things that are calculated in a ticks that isnt specific to a phase, for example rails and redstone dust is calculated recursivly independent from the ticks and can happen in all of them. These components are called "recursive updators" or "instant updators".
