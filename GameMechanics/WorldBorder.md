---
title: World Border
description: An analysis of the World Border - done in 1.16.4 by FX (PR0CESS)
---

# World Border

*The analysis is done in 1.16.4 by FX (PR0CESS)*


"World Border" - The visible barrier on the edge of the Minecraft world

"World Boundary" - An invisible wall 1 chunk further than the World Border at the edge of the world. This does not change when the world border changes its size.

## Appearance
The world border by default looks like blue stripes that are animated. These stripes will become red if the border is shrinking or growing. The border will always appear red in the nether dimension.

## Size
The world border is the bounding box that surrounds the Minecraft world and prevents you from going past the integer limit on the X & Z axes. The exact size at which the world border is set by default is: X/Z ±29,999,984 - Which is one chunk less than 30M. The center & size of the world border can be changed using the `/worldborder` command. The world border will always apply physics in full blocks, so even if it's halfway through a block internally it's still calculated per block. It will have different sizes based on which dimension you are in, these sizes are determined by the dimension shrinking value.

## Entity Collision
Most entities when hitting the world border will collide with it as if it's a block. Here is the list of entities that are not affected by the world border:
* All Fireballs
* All Throwable Potions
* XP Bottles
* Ender pearls
* Snowballs
* Eggs
* Arrows
* Tridents
* Wither skulls
* Ender Dragon
Entities can still be shot outside of the world border using a dispenser or dropper facing directly into the world border. All entities will act normally outside of the world border. Spiders can climb the world border, and enderman can teleport & place blocks outside of the world border.

### Getting outside the world border
There are many ways for players to go outside of the world border. Here are the easiest and most common methods:
* Throwing an ender pearl through the world border
* Using a valid spawning condition such as sleeping or dismounting an entity
* Eating Chorus fruit to teleport past the world border
* Using the /teleport command
* Allowing the world border to shrink past you

## Blocks
Blocks outside of the world border do not have a hitbox, this means players are unable to interact with blocks outside of the world border. Players cannot place or break blocks normally. Here are a few ways to place blocks outside the world border:

### Scaffold
Scaffolding does not check if it is being extended past the world border, therefore you can easily place scaffolding against itself to place scaffolding outside the world border.

<img width="50%" alt="Scaffolding Example" src="https://cdn.discordapp.com/attachments/719001038362771507/797115230743429180/2021-01-08_09.42.13.png">

### Beds
This is very simple, beds if placed directly on the world border extend one block in due to being 2 blocks long

### Placing against a block
You are able to place a block on the other side of the world border by placing the block against a block within the border. Trapdoors are usually the best block to do this with.

<img width="50%" alt="Against Block Example" src="https://cdn.discordapp.com/attachments/719001038362771507/797117021048274954/2021-01-08_09.57.24.png">
You are also able to place blocks against full blocks if the player is outside of the world border. This is because the world border is a float more into the world, which is why you can't interact with a block outside even if it's touching the border

### Sticky Piston Head
An interesting bug with the piston head can be used to place blocks on the other side of the world border. Just have a sticky piston facing into a block, facing into the world border, then power the sticky piston. Doing this with a regular piston will create a B36 as displayed in the B36 creation Method #2 below.

<img width="50%" alt="Piston Logic" src="https://cdn.discordapp.com/attachments/719001038362771507/797118844472655932/2021-01-08_10.00.51.png">

### Fluids
Fluids can pass freely through the world border as if it doesn't even exist.

### Chests
Chests outside of the world border can be accessed, if another chest is placed next to them, extending that chest's inventory outside of the world border where it can be accessed by a player.

## Moving_Piston (B36) creation using the world border
The world border is the only known place that a Moving_Piston (B36) can be created in 1.17. There are a few ways of creating a Moving_Piston (B36) this way. I also include the block data in case you might need it.

### Method #1 - [Piston Facing out]
<img width="50%" alt="Piston Facing Out" src="https://cdn.discordapp.com/attachments/793179568508108820/807335822226358282/huge_2021-02-05_14.29.14_0000.png">
__B36 Block Data:__ `{blockState: {Properties: {facing: "[dir]", extended: "false"}, Name: "minecraft:piston"}, x: [xPos], y: [yPos], z: [zPos], facing: [facing], progress: 0.0f, id: "minecraft:piston", source: 1b, extending: 0b}`

### Method #2 - [Piston pushing block against]
<img width="50%" alt="Piston pushing block against" src="https://cdn.discordapp.com/attachments/793179568508108820/807341516728303636/huge_2021-02-05_14.55.03_0000.png">
__B36 Block Data:__ `{blockState: {Properties: {[blockPushedProperties]}, Name: "[blockPushedName]"}, x: [xPos], y: [yPos], z: [zPos], facing: [facing], progress: 0.0f, id: "minecraft:piston", source: 0b, extending: 1b}`

This can also be done using a regular piston if the piston is 1 block closer to the border

### Method #3 - [Piston pushing in]
<img width="50%" alt="Piston pushing in" src="https://media.discordapp.net/attachments/793179568508108820/807318838139813948/WorldBorder_B36.png?width=1440&height=545">
__B36 Block Data:__ `{blockState: {Properties: {facing: "[dir]", short: "false", type: "[pistonHeadType]"}, Name: "minecraft:piston_head"}, x: [xPos], y: [yPos], z: [zPos], facing: [facing], progress: 0.0f, id: "minecraft:piston", source: 1b, extending: 1b}`

This can also be done with the piston being one block inside the world border, then blowing up the piston body using TNT

### Other Methods
Currently, all other methods are just different orientations or placement of these 3 methods.


### Blocks outside the world border
Most blocks work the same outside the world border. Except for Ticking tile entities. All scheduled ticks will continue working but tile ticks called every tick will not. This prevents Pistons from being able to retract, furnaces from smelting, hoppers from pushing items into containers, end gateways from extending their life, etc...

### Pushing blocks against the world border
If a piston pushes a block so it touches the world border, it can cause a Moving_Piston (B36) to appear, this Moving_Piston (B36) will display the block that got pushed, resulting in an effect where you can merge multiple block textures together.

### Pistons pushing past the world border
Pistons are able to push past the world border under certain conditions. If the piston is facing into the world border and the block on the other side of the border can be destroyed by a piston, then the piston will set its extended state to true, but will only place a Moving_Piston (B36) in front of the piston. When depowering this piston it will retract normally, although if you break this piston while it's extended, the Moving_Piston (B36) will stay. This Moving_Piston (B36) has an extended hitbox of a piston head where the Moving_Piston (B36) is located. Although its tile entity is rendered inside the piston block that just got broken since the tile entity's extended time is 0.

## Block Breaking Progression Saving
The world border is able to prevent block breaking progression from changing. You can do this by starting to break a block that is against the world border, then look at a block next to it that is on the other side of the world border but still touching the world border. Doing so will keep the block breaking progression on the block. You can then push another block in its place and the client will keep the progression, allowing you to break the block much faster than usual.

## The Final Boundary
The world border itself can be passed as explained above in Entity Collision. Once you pass the world border you enter a 1 chunk buffer before the actual world boundary. The world boundary is an absolutely impenetrable wall that can't be passed, located at exactly 30M.
In between the final boundary and the world border you will take damage, there is a 5 block safe zone in front of the world border, once you pass that you take 0.2 Damage for every block in. This means you take 2.2 damage per tick right on the world boundary.

## Technical Bugs related to the world border
1. [Block breaking progress can be saved using world borders](https://bugs.mojang.com/browse/MC-54587)

2. [Can place/take water/lava/lilypads outside world border (Ghost Block)](https://bugs.mojang.com/browse/MC-54119)

3. [Unable to use any items when aiming at a block that is outside the worldborder](https://bugs.mojang.com/browse/MC-220191)

4. [Blocks cannot be blown up past 30 million](https://bugs.mojang.com/browse/MC-223190)
