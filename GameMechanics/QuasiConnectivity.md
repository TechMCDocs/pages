---
title: Quasi connectivity
description: Quasi connectivity explained
---

# quasi connectivity

Quasi-connectivity (abbreviated to QC) is the behavior of pistons, droppers and dispensers to be powered if they would be powered (without QC) if they were 1 block above. A block being "powered by quasi-connectivity" is sometimes called "quasi-powered".
For example, consider the setup in the img below. On the right, both the piston and the redstone lamp are directly powered by the redstone torches, but on the left, the piston is powered by quasi-connectivity, and the lamp isn't because lamps aren't affected by QC. The piston on the left is powered because it would be powered without QC if it were 1 block above.

![qc1](https://i.imgur.com/V57RQMm.png)

Some players like to think of quasi-powerable components like the bottom half of a door, where the top half is invisible. If the top half of a door is powered, then so is the bottom half. Likewise, if the block above a piston is powered, so is the piston itself. Of course, this is only a way to make QC easier to understand, there are several differences between real doors and quasi-powered components.
## Budding
Since the rest of the redstone system isn't written with quasi-connectivity in mind, components that are supposed to power pistons with QC may not give out adequate block updates for the piston to realize that it is powered, until it receives a block update from elsewhere. For example, in the img below, the repeater sends out block updates as far as it expects redstone components to be, including the block in front of it and the blocks around that, but does not anticipate that there may be a piston being quasi-powered below, so does not send out block updates that far. The piston therefore needs a block update from elsewhere (e.g. the rails) before it realizes it's powered and extends.

![qc2](https://i.imgur.com/02vD7xr.png)

In some other cases, redstone components may naturally update pistons as they're quasi-powering them, because they are close enough. In the below diagram, both pistons are quasi-powered, but the one on the right doesn't need extra block updates to realize it's powered, because redstone dust naturally sends out block updates 2 blocks away, whereas the one on the left needed a block update from elsewhere to extend.
![qc3](https://i.imgur.com/EXGMp7L.png)


## Uses
Obviously, quasi-connectivity is convenient in some situations for redstone layout, but it can be unhelpful in that regard too. So this section covers deeper usages beyond simple layout, which is mostly related to budding.
BUD-powering can be used to introduce block event delay in zero-tick circuits [TODO: full article on block events including block event delay].
## Caveats
Many players who play Minecraft casually find quasi-connectivity (particularly the budding aspect) to be one of the most confusing aspects of intended redstone mechanics https://bugs.mojang.com/browse/MC-108 . This is particularly true of droppers and dispensers, where there is no visual indication when they have been budded on by accident, in which case they may not dispense when they are powered directly.
Another common mistake is running a redstone line on full blocks above pistons, droppers or dispensers, like in the img below.
![qc4](https://i.imgur.com/VE4OayD.png)
In this setup, the pistons are not only powered directly through the block above them, but also quasi-powered via each of the blocks diagonally above them. If (due to the update order of redstone dust [TODO: article]) the redstone directly above a piston depowers before the redstone adjacent to it, then a piston can go from directly powered to quasi-powered to budded and stay extended. A simple fix is to replace the full blocks with slabs, so that each pistons is only quasi-powered from the dust above it (which will update the piston when it turns off).
