---
title: Comparator signal strength
description: Mechanics of the comparator related to signal strenth
---

# comparator signal strength

The redstone comparator is a block which can maipulate signal strength outputs from other redstone components, certain blocks, and item frames.
- Normal Containers
For normal containers, the output of a comparator is governed by a simple equation:
Let I be the size of the inventory, 
i be the slot in the inventory, 
C be the number of items in the stack, 
S be the stack size (1, 16, or 64), and 
I_N be the number of slots in the inventory such that the C for that slot is non-zero. Assume that S for an empty slot is 1.
![enter image description here](https://i.imgur.com/1saTFkJ.png)
Note that C can be on the domain [1,127] regardless of the stack size. For this reason, it is possible to achieve signal strengths greater than 15 in 1.12 survival using stacked shulker boxes. All stacked boxes acquired in older versions will still be stacked if the world is updated to a newer version, however stacking boxes in newer versions is no longer possible without the assistance of mods or duping exploits.
Since no other redstone component can directly transmit a signal strength greater than 15, the raw transmission of these signals is therefore confined to the x-z plane.
Comparators cannot read from chests with solid blocks directly above them. If the block is placed or removed, it will still retain its state until updated.
The list of normal containers and their inventory sizes are as follows:
Furnace, Blast Furnace, Smoker: 3
Hopper, Brewing Stand*: 5
Dropper, Dispenser: 9
Chest, Trapped Chest, Barrel, Shulker Box: 27
Double Chest, Double Trapped Chest: 54
The number of slots in a brewing stand was changed from 4 to 5 in the 1.9 release with the addition of the blaze powder mechanic.

- Other Sources
Comparators can read signal strengths from other blocks, but use different rules for calculating the signal strength output for each one.

1. Command Blocks
Comparators will use the value of the 
SuccessCount
 tag directly. This tag is responsible for tracking the number of successful executions for the command it is currently set to hold. However, this value can be directly modified by using commands (
`/blockdata <x> <y> <z> {SuccessCount:<value>}`
for 1.8 - 1.12 and
`/data modify block <x> <y> <z> SuccessCount set value <value>`
for 1.13 and above).

2. Detector Rails
Comparators reading from detector rails will output a signal strength according to the equation in 
Normal Containers
 if there is a hopper or chest minecart above it. If there is a command block minecart above it, it will use the same rule as described in 
3. Command Blocks
If there are multiple of these types of minecarts on the detector rail, the oldest one takes priority. All other minecarts have no effect on the output.
4. Item Frames
Comparators reading from item frames will output a signal strength based on the rotation of the item in the frame, if any. If there are no items, the comparator will output zero. An item in the frame will change the output to one, and rotating the item will increase the output by one until it returns to the default rotation and resets back to one. The behavior for determining the signal strength for competing sources are different between versions: 
- pre-1.16: The value from blocks occupying the same space as the item frame always take priority over the item frame. Changing the signal strength output of the block without sending a comparator update (e.g. dispensing water, moving an open trapdoor, locking a chest with a solid block, etc.) will cause the comparator to retain its state until updated as described in the Normal Containers section. This effectively creates a dual-edge comparator update detector.
- post-1.16: The comparator will take the maximum value from any input which is not a redstone component, unless said redstone component is powered to signal strength 15 or above. Changing the maximum value without sending a comparator update (e.g. locking a chest with a solid block) will cause the comparator to retain its state until updated as described in the Normal Containers section similar to the pre-1.16 behavior.
5. Jukeboxes
If a disc is inserted into the juke box, it will output a unique signal strength depending on which disc is stored in it:
13 cat blocks chirp far mall mellohi stal strad ward
11 wait pigstep (post-1.16 only).
The signal strength is retained regardless of whether the song is still playing.
6. Cakes
A cake has a maximum value of 14, but decreases by two levels for each bite consumed (i.e. signal_strength = 14 - 2 * #bites).
7. Cauldrons
With water or snow*, the output represents the fill level, which can be in the range 
[0,3]
. Lava cauldrons will always output one (post-1.17 only).
8. Composters
The output represents the fill level, which can be in the range 
[0,8]. Note that level 7 is unstable and will transition to level 8 after exactly one second (20 game ticks) after level 7 was reached.
9. Lecterns
The output represents which page the book is on as a fraction of 15, where the first page is 0 and the last is 15. The signal strength is precisely (page_index/max(1,total_pages-1)) * 15.
10. Beehives/Bee Nests
The output represents the fill level, which can be in the range [0,5].
11. Respawn Anchors
The output is max(0,4*#charges-1).
12. Skulk Sensors
Comparators will output a different signal according to which sound the sensor detected. The current values are not yet final, so they will not be listed here.
13. Other Redstone Components
Comparators will preserve whatever signal strength is given as input.
## Comparator Mechanics
Given multiple redstone inputs, a comparator will take the one with the maximum signal strength.
Block-Entity Priority: 
- pre-1.16: Comparators always prioritize the block.  
- post-1.16: Comparators prioritize the highest signal strength.

1. Block/Entity-Component Priority: Comparators prioritize Components if and only if their signal strength is 15 or greater.
2. Component Signal Strength Priority: Comparators will prioritize based on directional update order.
3. Comparison/Subtraction Priority: Comparators will use the largest of the two side values.
4. Comparison Logic: Comparators will always output zero if the back input is less than at least one of the side inputs.
5. Subtraction Logic: Comparators will output according to the rule: 
out = max(0,in - max(left,right)).