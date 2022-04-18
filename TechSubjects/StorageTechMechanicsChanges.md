---
title: Storagetech version history
description: Mechanics changes related to storage tech
---

# storagetech version history

A list that documents changes in newer versions that are relevant to Storage Tech.

## 1.11
+ Added Shulker Boxes.
+ Added Observers.

## 1.12
+ Added Glazed Terracotta.

## 1.13
+ Noteblocks are no longer block entities. They can now be pushed by pistons. They also have 3 new block states (note, powered, instrument), which means they can interact with observers, and they emit block updates when powered or right-clicked by a player.
+ Leafstone: leaves have a new blockstate called 
distance, a 0-7 value which refers to the distance of the leaf block from the closest log. When a log is placed/removed/moved leaves update sequentially with a 1gt delay.
+ Chests can now be horizontally placed next to each other. Before you had to alternate normal and trapped chests.
+ Many blocks can now be waterlogged.
+ Added Bubble Columns.
+ Added Dead Coral Fans.
+ Added Sea Pickles.
+ Added Dolphins.
+ Shape change of walls, fences, glass panes and iron bars can be detected by observers.
+ Sticky pistons that fail to pull blocks don't send out block updates in front of them.
+ Sloped powered/activator rails give out more block updates.
+ New water mechanics.
+ Locked chests can now interact with hoppers.
+ Hopper's hitbox and collision box have been changed.
- Water doesn't emit an observer output anymore when it receives an update.

## 1.14
+ Added Scaffolding.
+ Added Composters.
+ Added Barrels.
+ Added Lecterns.
+ Shulker Boxes are now solid.
+ Shulker Boxes opening/closing can be detected by observers.
+ Walls now connect to more transparent blocks (including pistons and glass).
+ Redstone components can now be placed on top of transparent blocks such as glass, glowstone, sea lanterns, ice and top trapdoors.
+ Shears can now be used by dispensers.
- Redstone components and rails can no longer be placed on top of soulsand.
- Comparators can read the content of item frames even if there is water in the same block: item frame CUDs aren't possible anymore.

## 1.15
+ Added Honey Blocks.
+ Bells are now redstone components.
+ Armor Stands can now be placed by dispensers.

## 1.16
+ All redstone components can now be placed on the back of pistons.
+ Redstone components and rail can once again be placed on top of soulsand.
+ Added Target Blocks.
+ Added Respawn Anchors.
+ Wallstone: vertical instant lines using walls, trapdoors/fencegates opening/closing cause a shape update to a wall below.
+ Saddles and horse armor can be equipped by dispensers.
• Redstone Dust Changes: 
https://www.minecraft.net/content/dam/games/minecraft/screenshots/snapshot-20w18a-carousel2.jpg

## 1.17
+ When a Shulker Box item is destroyed its content are now dropped.
+ Added Powder Snow.
+ Rails are now waterloggable and don't get destroyed by fluids.
+ Bubble columns now take 5 more ticks to update when soulsand or magma blocks are removed.
