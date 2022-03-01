---
title: Piston
description: Piston mechanics
---

# piston

## Activation mechanics
A piston starts extending or retracting in the 
block event phase of a tick. A piston can be powered by a block directly adjacent to it or by quasi-connectivity (by a block that would power the block above the piston). When a piston is updated, it checks if it should retract or extend. If it can, it creates a block event containing its position and what action the piston should do, if the blockevent doesn't already exist (except in 1.15- where blockevents doesn't have a proper hashcode, so one block can have multiple blockevents).  Note that a piston trying to extend can fail (even if later, in the blockevent phase, it can extend) even though a retraction can't fail (because in the worst case, it retracts without pulling a block). When the block event phase starts, the game loops through the blockevent list, and executes blockevents in the order of creation (first in, first executed). When the piston executes its block event, it checks again if it can still do its action. If it is trying to push, it checks that it is powered and not blocked. If it is trying to pull, it checks if it is unpowered.

## Movement
A piston initiates its extension in the same tick it gets activated (tick no. 0), it replaces itself and the destination blocks of the blocks pushed with a 
moving piston (block 36). 2 ticks later (in tick no. 2), a piston is placed back where it was before (except now it is extended) and the pushed block arrive at their positions. When the piston gets deactivated it immediately (tick no. 5) starts retracting even if it was already in the process of extension. It replaces itself with a moving piston. If it is a sticky piston, it also tries to pull blocks and places moving pistons at their destinations. 2 ticks later (in tick no. 2) a piston is placed back where it was before and the pulled blocks arrive at their positions. If the extension of a sticky piston is interrupted (by its retraction), the directly pushed block arrives immediately (in the same tick as the retraction starts). The retraction does not pull the block and therefore it is dropped. This can be done using a 0-tick pulse, 1-tick pulse or 2-tick pulse. The indirectly pushed blocks arrive at the same time they would normally.

## Microticks
A 0-tick pulse can be said to have a certain duration in microticks. A microtick pulse duration determines how many blockevents can execute before the pulse ends. A 1-microtick pulse does not affect pistons at all, since by the time the blockevents are executed the pulse has already ended and therefore cannot power the piston. A 2-microtick pulse is enough to power pistons that were updated by the rising-edge of the pulse. A 3-microtick signal can power pistons updated by the execution of the blockevents created by the pistons updated by the rising-edge of the pulse and so on...

## Pushed blocks order
When the piston starts extending or retracting, it creates a list of blocks to move and a list of blocks to break. For this, it first searches all blocks to push in the line of blocks from the nearest to the furthest or to pull from the furthest to the nearest. If these blocks stick to others blocks, they are stored in the -y;+y;-z;+z;-x;+x and it search again the blocks in the line of the added blocks. Then the game loop through the blocks list in **reverse** order, creating moving blocks and sending block updates where they were. So blocks update from the furthest (in number of lines to get to this one) lines in the piston direction, if it's the same distance, it's in the +x;-x;+z;-z;+y;-y
 order (-y;+y;-z;+z;-x;+x reversed) and in a line it's from the furthest to the nearest when pushing and from the nearest to the furthest when pulling (reversed here again).

## Block updates
The game first creates moving blocks in front of each moving blocks,sending some state updates at the position of the new block, following the update order explaned above, and then delete all leftover blocks, reading from a hashmap,so the order of the state updates here is locational. Finally it send redstone block updates around all removed blocks and the movin piston head
