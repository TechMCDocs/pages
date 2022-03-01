
---
title: Block updates and update detectors
description: Description and usage of block updates and update detectors
---

# block updates and update detectors

Minecraft has a game mechanic called Updates. Redstone Components, liquids, sand, and many other things rely on these to determine if something needs to be done.
When you flick a lever adjacent to a trapdoor, the trapdoor will change state. It receives Block Updates, which make it see if it's not in the state it should be in, and then it changes state to the state it thinks it should be. 

However, in certain cases, something affected by updates will be in a condition where it should do something, but needs an update to realize that it needs to do something, despite the update not being what made it need to do something.  While some of these may seem like bugs, with some having been reported on the bug tracker, many are just part of maintaining game performance or original behavior, generally in redstone where it's frequently used.

In the code block updates are a call that execute a function specific to the block. This call itself can send other block updates if the block changed and need to update neighboring blocks. This create a recursive call and stop until there no block to update. Each call in another call add memory in the program stack. If the calls go too far in depth, it will consume all the memory in the stack and create a stack overflow error, causing update suppression.

To make a block update detector you may simply place a sticky piston facing up, with a single slime block on top of the piston and a redstone block on top of the slime block. Then when a block update occurs on the piston it will extend and then retract unless further updated.

## Types of updates:
1. Block updates, aka Neighbor changed updates, redstone updates, sometimes just "updates":
Most of the time they are used to update a block when their received power can potentially change
What creates Block Updates?
- A block going from being one block to another (By a player placing a block in a replaceable location, by a fill command, by falling_block entity hitting the ground or a gravity block on tile tick deciding to fall, by trees setting blocks, changing a block into a different block, air being replaced by Moving_piston, a dispenser removing or placing a liquid or fire)
- Placing and removing of redstone components and rails (such as by a player or by a piston, including converting it to Moving_piston) to wherever they could power or for rails around the block they're on and themself. This makes it so that nondirect neighbors to the placed component adjust themselves properly
- Some components changing state, including but not limited to powered rails becoming powered (around the block they're on, themself and if sloped around the block above), mobs setting the trigger for pressure plates and tripwire, repeaters on changing their power or their delay, comparators on having a different input or changing their mode, note blocks and bells on changing power (1.13+), and redstone dust on changing its power level, bubble columns being broken or created, scaffolding changing its distance, or a waterlogged block no longer being waterlogged
- A tile tick being executed and sending updates despite no state being changed, with tripwires and comparators
- What can Block Updates do?
On receiving a block update, blocks may
-Change state (trapdoors, rails, droppers, ect.)
-Break/remove themself (Rails, redstone wire, repeaters, tnt, ect.)
-Schedule tick if it should change state (repeaters, comparators, ect.)
-Schedule tick (Liquids)

2. State update, observer update, shape update, post placement (pp) update:
These are used to handle blocks reacting to other blocks in some non redstone ways.
What creates State updates?
Typically, a block changing state (as can be seen on the right side of the debug menu when looking at a block), sends state updates. This is with exceptions: blocks generated, loaded by chunks, sticky piston heads being removed while retracting, moving powered observer turning into block and other that can be found here: https://bugs.mojang.com/browse/MC-107664 .
While most of the blocks that send state updates also send block updates, some components such as trapdoors do not.
Redstone dust sends state updates diagonally adjacent in some conditions, but will not trigger observers in that location.
What can state updates do?
- Change state (walls, redstone dust, repeaters's locked state, concrete into concrete powder)
- Break/remove self (carpet, coral, nether_portal, basically non redstone blocks supported by a surface)
- Schedule ticks (falling blocks, liquids, scaffolding, leaves)
State updates have some properties regarding the direction of source. For the many non redstone supported blocks, they need an update from the surface they're on to be updated. 
Repeaters for locking and redstone dust can be updated from multiple directions, snow and others can be updated from any direction.

3. Comparator updates
These are used to update comparators without sending block updates, although comparators do respond to block updates as well.
What creates Comparator updates?
A composter or cauldron changing fill level
Changes in what is within an inventory
A hopper trying to pull items, or a detector rail which is colliding with an entity
Comparator updates can be detected only with a comparator, and only if the comparator is horizontally adjacent or 1 block horizontally away with a conductive block between the relative component 

## How do we detect updates?
A block which is in a state where it will react to block updates is called "Budded". BUDs are redstone contraptions which react to block updates and reset after receiving block updates. There are circumstances which will lead to a block being budded which are not practical to be reset and are not addressed here.
1. For solely block updates:
Examples:
- For some redstone components: 
- - Pistons can be budded by being unable to extend upon being powered, or when retracting into a powered position where they cannot extend.
Droppers, Dispensers, and pistons can be budded by Quasi Connectivity.
Powered rails and activator rails can become budded due to their circumstances of deciding when they are no longer powered.
In the case of a piston pushing what was powering it, before the piston finishing extending.
- - Rails may be budded for which direction or slope they need to be, specifically with moving rails around.
- For all redstone component that react to received power:
- - By moving or removing a detector rail into/out of a location where it would power something through the block it's on, as that does not send non adjacent updates so long as it doesn't change powered state.
- - By powered redstone dust changing its direction, making redstone components budded by the direction change sending no block updates.
- - By a target block reaching a destination after having a tile tick sending no updates on reaching the destination going into a location where it would be powered by redstone dust, ect. 

2. For solely state updates:
- Observers, from the face, will react to them.
- LUDs featuring waterlogged blocks
- Falling block floating setups, such as ones made from how a sticky piston failing to retract a slime structure sends no updates.
- Scaffolding which hasn't realized it's on an observer or target block due to them being moved after reaching a destination sending no updates.

3. For block updates and state updates:
- LUDs not featuring waterlogged blocks react to both of these
- Floating falling blocks, with 

4. For comparator updates and block updates:
- Chests being locked so a comparator can no longer read them, either by a solid block on top or some blocks, such as cauldrons being removed non adjacently
- A comparator budded with a general bud technique
See also:
 https://minecraft.fandom.com/wiki/Tutorials/Comparator_update_detector
