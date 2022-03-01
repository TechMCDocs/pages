---
title: Rail budding
description: Rail budding mechanics and usages
---

# rail budding

## Powering mechanics

When a rail is updated the rail dont send futher updates if it is already in a correct state(dont need to power or depower)
Block updates notifiers are always from above to below,top notifier if the rail is sloped,then notifier on self, and finally notifier below.
But if you only push the rail without making it change state, it will only send updates around itself
When a rail power, unpower, it send updates around the block below, and above if it's a sloped rail, to notify rails going up or down of the power change.
For that, it search a power source through the rail if they are in the same way and is pointed by the previous rail(upward if sloped,else same level or below), in a limit of 9 rail including itself.
You can have a situation where a rail point to another one,but that one doesn't point to it,that cause the rail search power only in one way, and act like a diode.
In some cases,the rail can't updates another rail that is pointing into it,that cause a bud of the rest of the rails.
You can place a power source,place another one where the rail is already powered,remove the first one,and no rails will unpower,because the update doesn't change the rail,and doesn't create futher updates,but some of them are budded, because they dont have power source at 9 blocks.

general bud method such as dust redirection and moving detector rail also work on rails.

## Instant bud wires:
its possible to create instant bud wire by budding a long rail line, and use a mechanism to rebud the entire line.
That can be done by detecting the rail depowering and rebudding the rail here, with redstone dust redirection,or moving detector rail
Or with moving power source,for that we can use the update order of rails,that update observers. Observers repower the entire line and depower with always the next observer powering there, and end at a permanently powered tail,without updating budded rails. 