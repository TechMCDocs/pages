---
title: Iron golem spawning mechanism
description: iron golem spawning mechanism
---

# iron golem spawning mechanism

## Quick Overview on Iron Golem Spawning
Barring manual spawning, Iron Golem can naturally be spawned by villagers under two circumstances:

- Gossip
- Panic

Gossip is slow so most iron farming uses the panic method. Panicking will trigger spawning instantly so it is way more faster. In this article, I will go over how the panic way works. This article is based on Minecraft Java 1.17.1 and Fabric Yarn mapping.

## What is Panic and When Will It Happen: The Brain
"Panic" is triggered by `PanicTask`. A task is a conditional, time limited and circular long running process in the Brain. Let's talk about Brain first, as golem spawning heavily depends on the brain mechanism. Brain handles lots of things: memories, sensors, tasks, activities and more.
In every game tick (Brain#tick), the Brain will do the following things in sequence (which means that they will be on the main thread):
1. tickMemories : Reduce memories' expire time by one and forget it they are expired after ticking. See Memories below.
2. tickSensors : Sense stuff periodically. All sensors are ran in sequence. This mechanism will be used in detecting hostile mobs and golems. See Sensors below.
3. startTasks : Check every registered tasks and start them. They must have a matching activity and stopped. See Tasks below.
4. updateTasks : Check every running tasks, tick them (if they can continue to run) or stop them (if they should stop).

### Memories
Memories are persistent key value pairs with optional expiry times (if not specified then `Long.MAX_VALUE`) stored in Brain. Keys are `MemoryModuleType<T>` while values can be anything with type T. Expire times are in ticks, represented by long. Brains can remember memories at any time (Brain#remember) and they will be checked for expiry at any tick (tickMemories).
All "remember" below means remembering a memory module with a value in the brain.
In golem spawning, the most significant one is 
`MemoryModuleType.GOLEM_DETECTED_RECENTLY` (with a Boolean value and 600 ticks expiry time, see GolemLastSeenSensor#rememberIronGolem). It will be remembered when:
- A villager participated in iron golem spawning (talk about that later), or
- A villager detected an iron golem (in GolemLastSeenSensor) in a 16 x 16 x 16 range from itself (in NearestLivingEntitiesSensor). See the Sensors section below.

Other important memory modules for iron golem spawning are `MemoryModuleType.MOBS` and `MemoryModuleType.NEAREST_HOSTILE`. They not only display whether there is a golem nearby, but also shows whether there are hostile mobs and will be used to trigger panicking.

### Sensors
Sensors are ticked periodically after all memories are ticked. Every sensor have a period time, which defaults to 20 ticks, but can be customized (for example, GolemLastSeenSensor runs every 200 ticks). Sensors will not start instantly when the brain starts ticking, since the games adds a random starting delay in every sensor constructor (this.lastSenseTime = RANDOM.nextInt(senseInterval);). Because of that, sensors won't start and run simultaneously (all in the same tick). For example, even if lots of sensors have the same period, they will run in different ticks and the order is undefined.

The most important sensors in iron golem spawning are NearestLivingEntitiesSensor, VillagerHostilesSensor and GolemLastSeenSensor, while the first two scans for all living entities (golems and hostile mobs) and the last one checks for the first's result and detect if there is already an existing golem nearby. 
#### NearestLivingEntitiesSensor
Runs every 20 ticks.
It will look for all living entities in a 16 x 16 x 16 box from the villager. Then, it will store its results to the MOBS and VISIBLE_MOBS memories, which will NOT expire.
How to determine if a mob is visible: TODO (NearestLivingEntitiesSensor#testTargetPredicate)
#### VillagerHostilesSensor
Runs every 20 ticks. If it is ran in the same tick with NearestLivingEntitiesSensor, it will be started after the NearestLivingEntitiesSensor done detecting.
It will check the VISIBLE_MOBS memory from NearestLivingEntitiesSensor and compare each of them to a predefined key value pair that contains all hostile mobs and their endanger range (checked using squaredDistanceTo). If it finds any, it will remember the NEAREST_HOSTILE
 memory. This memory will NOT expire. It is later used for panic determination.
#### GolemLastSeenSensor
Runs every 200 ticks. If it is ran in the same tick with VillagerHostilesSensor, it will be started after the VillagerHostilesSensor done detecting.
It will check the MOBS memory and see if there is a golem in it. If there is, it will remember the GOLEM_DETECTED_RECENTLY memory for 600 ticks.
### Tasks
Task is a complex system. Tasks are conditional, circular, time limited and long running processes in brains. Examples: 
- PanicTask
- WakeUpTask
- OpenDoorsTask

Tasks are conditional. Each task has an Activity key. If the brain does not contain a possible activity that matches the activity key in the task, this task will not run. For example, villagers have Core, Idle, Meet, Rest, Panic, etc. activities. Core tasks will always run regardless of what activity the villager is in. Fortunately, the only task related to golem spawning is PanicTask and it is a core task.
They are conditional in other ways, too. Each task has a list of required memories and unique (overridden) checking methods. Tasks will only be started it they match all these conditions.
Tasks are long running as well. They will keep running if additional conditions are met in every game tick. However, they are time limited. Each task has a hard coded minimum run time and maximum run time. When they are started, the game will randomly choose an end time between the min run time and max run time.
They are also circular: after a task stops, the brain will attempt to start it again in the next tick if activity matches.

Flow chart:
1. Brain is ticked. It calls startTasks method.
2. Brain iterates through all tasks.
3. For each task, it checks whether the brain's possibleActivities contains an item matching activity requirements for the task. If not, skip. For villagers, the possibleActivities list always contains Core.
4. Check if the task is already running. If so, skip.
5. Call the Task#tryStarting method. The task will call the hasRequiredMemoryState method to check if the brain contains all required memories. If not, return.
6. The task will call the 
shouldRun method. It defaults to true but is heavily overridden to meet custom requirements for each task. If not, return.
7. Mark the task as running.
8. Randomly choose an end time from min run time to max run time. Both values default to 60.
9. Call the overridden run method. Indicating the task is started running in this tick.
10. ... Start other tasks. Then the startTasks method is returned.
11. Brain then calls updateTasks, which ticks every running tasks.
12. Brain iterates through all running tasks. For each of them, it calls Task#tick.
13. Task checks whether the world time is greater than its end time. If so, it will call the stop method to mark it as stopped and then call finishRunning (overridden method).
14. Task calls shouldKeepRunning
 (overridden method) to check if it should continue running. If NOT, it calls stop as well.
15. Task calls keepRunning (overridden method) to tick itself.

#### PanicTask
For iron golem spawning, we need to focus on PanicTask. It has the following properties:
- Min and Max run time: 60 ticks. Therefore, it will always run every 60 ticks, regardless of randomness.
- Required memory states: none.
- Required activity: Core. This means that it will always run, regardless of activities (unlike gossip).
- shouldRun (whether the task will START to run, NOT CONTINUE running): always true. This means that it will always call run(), but not continueRunning.
- run, shouldKeepRunning and keepRunning implementations: too long to describe here. See below.

1. run:
 (when the task is started): Checks whether its brain contains HURT_BY memory OR whether its brain contains NEAREST_HOSTILE
 memory. If so, set the brain for panic activity. This will:Forget PATH, WALK_TARGET, LOOK_TARGET, BREED_TARGET and INTERACTION_TARGET memories.
2. doExclusively(PANIC): Change the activity to panic. (The two actions above have nothing to do with golem spawning)
Keep in mind that run will only be ran once every 60 ticks.
3. shouldKeepRunning: Checks whether its brain contains HURT_B memoryOR whether its brain contains NEAREST_HOSTILE
 memory. If not, stops the task in this tick and prevents future continueRunning calls.If shouldKeepRunning returns true, then keepRunning
 will be called. If the current world time is a multiple of 100, it will call the villager's summonGolem method. I will describe that later.

### Conclusion
This is a brief conclusion for how the game makes villagers panic and attempts to spawn the golem.
- For every 20 ticks, look for all living entities in a 16 x 16 x 16 box from the villager, then store it into MOBS and VISIBLE_MOBS memories.
- For every 20 ticks, iterate through VISIBLE_MOBS and checks if there is a hostile nearby. If so, remembers NEAREST_HOSTILE.
- For every 200 ticks, iterate through MOBS and check if there is a golem nearby. If so, remembers GOLEM_DETECTED_RECENTLY for 600 ticks.
- For every 60 ticks, if the brain remembers NEAREST_HOSTILE or 
HURT_BY, AND the current world time is a multiple of 100, call the villager's summonGolem method.

## Spawning Golems
We have analyzed when the game will try to summon golems. Now I will introduce what happens in the summonGolem method. summonGolem is a member method of VillagerEntity, so we assume all operations below are executed as the villager that is panicking.
1. Firstly, it will check whether the villager can spawn golems (VillagerEntity#canSummonGolem). This depends on whether the villager had slept recently and whether it remembers GOLEM_DETECTED_RECENTLY.
2. When a villager sleeps (VillagerEntity#sleep), it will remember the MemoryModuleType.LAST_SLEPT memory module with the value of current world time. This is later used in detecting whether the villager is slept recently.
3. The canSummonGolem method callsVillagerEntity#hasRecentlySlept method, which returns true if the last slept time (LAST_SLEPT memory) exists and its difference with the current world time is lesser than 24000 ticks (one game day). If it returns false, which means that the villager has not been slept for a day, the methods returns false and the spawning process aborts.
4. The canSummonGolem method will consequently check if GOLEM_DETECTED_RECENTLY memory exists. Remainder: that this memory will expire after 600 ticks and will expire before tasks got ticked (so basically before calling summonGolem method). If it exists, it indicates that the villager has just spawned (or participated in spawning, which we will talk about later) a golem within 600 ticks and the spawning process will abort. Now, the current villager is capable of spawning a new golem. 
It will then ask two more villagers to participate in that process. 

The flow is:
1. Get all villagers that are in a 10 x 10 x 10 box from the current villager. This includes itself. (getNonSpectatingEntities) Remove villagers that cannot summon golems from the list using the summonGolem method, which means its requirements are same as the current villager.
2. Check if the remaining list has >= three villagers. Three is the number of required villagers for panic spawning. For gossip spawning, it requires five. Remember, the list includes the current villager itself. If there are less than three villagers, the spawning process stops.
3. Summon the golem: spawnIronGolem.
4. For each participating villagers, force them to remember the GOLEM_DETECTED_RECENTLY memory for 600 ticks, regardless of whether they detected a golem.

### spawnIronGolem
TODO

### Conclusion
Let's wrap up what happens after a villager is panicking:
1. Checks if the villager has slept within 24000 ticks (one game day).
2. Checks if the villager does not remember the GOLEM_DETECTED_RECENTLY memory.
3. Find other two villagers that matches the two conditions above within 10 x 10 x 10 from the current villager.
4. Spawn the golem.
5. Force himself and other two villagers to rememberGOLEM_DETECTED_RECENTLY for 600 ticks.

## Iron Farm General Design Tips & Critical Factors & Conclusion
Through the analysis, we know that:
- A villager scans a 16 x 16 x 16 box for hostiles (must be directly visible and within a specific range which is different for each mob) and golems.
- If a villager detects a golem within that range, it stops spawning for 600 ticks.
- If a villager finds a hostile, it will panic and try to spawn.
- Including the panicking villager, it requires three villagers within 10 x 10 x 10 from the panicking villager to spawn a golem.
- All three villagers should have been slept within 24000 ticks.
- All three villagers must not notice a golem in 16 x 16 x 16 range in the last 600 ticks.
- After spawning, they need to wait for another 600 ticks to spawn another one, even if there is not any golems in their detecting range.

Therefore, when designing iron farms, it is important to keep in mind that:
Core: A group of three villagers.
- Keep cores apart. Do not leave the spawning area within other groups detecting range, or spawning will affect other cores. Another solution is to let cores spawn golems simultaneously (panic the cores all together).
- Move golems outside of villagers' detecting ranges as soon as possible. An extreme practice would be nether portals.
- Kill golems outside of villagers' detecting ranges. Killing takes time. If they are burnt within detecting ranges, all villagers will continue refresh their 600 ticks timer.