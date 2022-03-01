---
title: Zero tick farms
description: How a zero tick farms work
---


# Zero tick farms

A zero tick farm is a farm that relies on replacing the block a crop (which grows on top of itself) is resting on, which forces it to grow a stage. This but however was patched in 20w12a and does not work in 1.16+.

## mechanic
deleting a block the plant is resting on schedule a tile tick on some crops that doesn't break instantly. It will check 1 gt later if it break itself.
But in the case where you replaced a block before this check, it will execute the rest of the tile tick that grow the plant, that is normally called on a random tick