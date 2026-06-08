---
title: Piston
description: Piston mechanics
---

# Piston
The piston is a block that, when powered, moves other other blocks or entities.

## Activation mechanics
A piston can be powered by a block directly adjacent to it or by quasi-connectivity (by a block that would power the block above the piston).

When a piston is updated, it checks if it should retract or extend. If it should, and it is not pushlimited, it creates a block event containing its position and what action the piston should do, as long as the block event doesn't already exist (except in 1.15). Note that a piston trying to extend can fail, while a retraction cannot (if pushlimited, it will retract without pulling a block).

When the block event phase starts, the game iterates through the block event list, and executes block events in the order of creation. When the piston executes its block event, it checks again if it can still do its action. If it is trying to push, it checks that it is still powered, and not blocked. If it is trying to pull, it checks if it is still unpowered. This means that a pulse that turns off before the block event phase will not cause a piston to extend.

## Movement
When a piston is activated, it replaces the destinations of the blocks pushed with a moving piston block (block 36). If it is extending, then it sets its block state to extended, and replaces the block in front of it with a moving piston block corresponding to a piston head. If it is retracting, it replaces itself with a moving piston block with the entire hitbox of an extended piston (head included). At this point, none of the hitboxes have changed, although the actual blocks have been replaced. 

Then, in the block entity phase for the next two ticks, the moving piston blocks will move their hitboxes (pushing entities).

After two ticks have passed, the pushed blocks arrive at their positions, and convert back into their original blocks, in the block entity phase.

When the piston is updated and sees that it is unpowered and extended, it immediately starts retracting, even if it was already in the process of extension. If the extension of a sticky piston is interrupted (by its retraction), the directly pushed block arrives immediately (in the same tick as the retraction starts). The retraction does not pull the block and therefore it is dropped. This can be done using a 0-tick pulse, 1-tick pulse or 2-tick pulse. The indirectly pushed blocks arrive at the same time they would normally.

## Pushed blocks order
When the piston starts extending or retracting, it creates a list of blocks to move and a list of blocks to break. For this, it first searches in the line of blocks in front of the piston, from nearest to farthest (if push) or farthest to nearest (if pull). If these blocks stick to other blocks, they are stored in the order -y;+y;-z;+z;-x;+x and it searches again for blocks in the line of the added blocks.

Then the game loops through the list of blocks in **reverse** order, creating moving blocks. So each line of moving blocks is iterated from farthest to nearest (relative to the movement direction). The lines are iterated in order of distance from the main line (in front of the piston); if two lines are at the same distance, the tiebreaker is +x;-x;+z;-z;+y;-y order (reversed from before).

## Block updates
The game first creates moving blocks ([B36](/pages/Blocks/MovingBlock36)) in front of each block to be moved. It then, following the update order explained above, sends state updates at the position of the each of the new blocks. Next, it deletes all the old blocks by reading from a hashmap, so the order of the state updates here is locational. Finally, it sends block updates around all removed blocks and the moving piston head.
