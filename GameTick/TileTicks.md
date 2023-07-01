---
title: Tile Ticks
description: What happens in tile tick phase?
---

# tile ticks

Tile ticks are actions sheduled to happen in a given number of ticks at a block.
When the tile tick is executed, the block executes an action depending on its type.

## Tile tick phase
In this phase the game selects scheduled tile ticks which have their processing tick (the result of taking the tick number(world counter) from the tick the update was scheduled in + the delay of the tile tick) smaller than or equal to the current tick, and move them to a different list, where they are executed. Some redstone components such as observers and lamps doesn't check if there is a tiletick in the executing list before scheduling a new one:  
[[MC-189954] Observers react to updates if they already have a scheduled tick](https://bugs.mojang.com/browse/MC-189954).

There are two different schedulers that get processed after one another: block tile ticks, then fluid tile ticks.
### Tile tick cap:
A maximum of 65536 tile ticks can execute in one tick. The tile ticks that weren't executed in the tick they were supposed to are delayed to the next tick.

### Player input bug:
Player input are executed after the block event phase and before the world counter increment, so if a player input creates a tile tick, it will schedule a tile tick starting from the current tick, and execute the block event phase only in the next tick.
Whether the player inputs start at the end of a tick or at the beginning of the next tick is not really defined, because it depends where you split ticks.
If you consider that a the player input phase happens at the beginning of a tick, then tiles tick scheduled by a player input lose 1 gametick of delay.
If you consider that a the player input phase happen at the end of a tick, then blockevents created by a player input will execute(extend a piston for example) only in the next tick.

### Delay length:
| Name | Delay |
|---|---|
| Repeater | 2, 4, 6, 8 |
| Comparator | 2 |
| Redstone Torch | 2 |
| Observer | 2 |
| Lectern | 2 |
| Lightning Rod | 8 |
| Target | 8 |
| Dispenser | 4 |
| Dropper | 4 |
| Redstone Lamp | 4 |
| Stone Button | 20 |
| Wooden Button | 30 |
| Stone Pressure Plate | 20 |
| Wooden Pressure Plate | 30 |
| Weighted Pressure Plate | 10 |
| Detector Rail | 20 |
| Tripwire Hook | 10 |
| Tripwire | 10 |
| Sand, Anvil, Concrete powder | 2 |
| Dragon Egg | 5 |
| Composter | 20 |
| Big Dripleaf | 10, 10, 100 |
| Big Dripleaf Stem, Weeping Vines, Weeping Vine Plants, Twisting Vine, Twisting Vine Plants, Sugar Cane, Kelp, Kelp Plant, Bamboo, Chrous Plant, Chrous Flower, Cactus, Cave Vines, Cave Vines Plant | 1 |
| Command Block | 1 |

### Tile tick priority:
A tile tick have a priority. If multiple tile ticks are scheduled to be executed, the tile tick with the higher priority (lower value) will execute first.
It is only relevant to repeater and comparator, as everyhting else has default priority of 0.

```
SCHEDULED TICK PRIORITIES

REDSTONE DIODE?    - YES    - REPEATER    - FACING ANOTHER DIODE?   - YES                   - PRIOTITY: -3
                                                                    - NO     - POWERING     - PRIORITY: -1
                                                                             - DEPOWERING   - PRIORITY: -2
                            - COMPARATOR  - FACING ANOTHER DIODE?   - YES                   - PRIORITY: -1
                                                                    - NO                    - PRIORITY:  0
                   - NO                                                                     - PRIORITY:  0
                   * SPECIAL CASES  - UNPOWERED REPEATER IS NOT RECEIVING POWER WHEN TICKED - PRIORITY: -2
                   ** 'FACING ANOTHER DIODE' REALLY MEANS 'FACING ANOTHER DIODE THAT IS NOT FACING IT'
```
