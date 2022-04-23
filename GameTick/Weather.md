---
title: Weather
description: How weather works in extreme detail
---

# Weather

## Weather Cycle
In the weather section of the tick function there are 6 variable allocations.  
The first one is a variable you get from a function that decides if it’s raining or not from rain gradient (the sky colour, but more later on this). The other variables are clearWeatherTime, rainTime, thunderTime, isRaining and isThundering from the world properties.  
The first part of the code is an if block that checks if `clearWeatherTime` is bigger than 0. This part is basically a hackfix to make it so that the weather will stay clear for a certain amount of time when the `/weather clear [time]` command is used. In this block `clearWeatherTime` will count down. After this `thunderTime` and `rainTime` will be set to 1 if it’s not thundering or raining respectively. If it is thundering or raining they will be set to 0. After this `thundering` and `raining` will be set to false in the properties.  
What this accomplishes will be that once `clearWeatherTime` has reached 0, it will start raining and thundering the next tick.
- If `clearWeatherTime` isn’t bigger than 0, `thunderTime` and `rainTime` will be checked.
- If `thunderTime` is bigger than 0 we’ll check if `thunderTime - 1` is equal to 0. If that is the case, then `thundering` will be reversed. This means if it was thundering, it will stop thundering and if it wasn’t thundering it will start thundering.
- If however `thunderTime` isn’t bigger than 0, a new value is set for thunder time.

This value is decided by:  
`thunderTime = thundering ? this.random.nextInt(12000) + 3600 : this.random.nextInt(168000) + 12000;`  
In a more readable format, this will mean that:
- If it is thundering, the new `thunderTime`  will be between 3 600 and 15 600.
- If it is not thundering, the new `thunderTime`  will be between 12 000 and 180 000.

Which value somewhere in this range it becomes is determined by a random factor.  
The same process happens for `rainTime`, though the new value for `rainTime` is determined differently:  
`rainTime = propertiesRaining ? this.random.nextInt(12000) + 12000 : this.random.nextInt(168000) + 12000;`  
In a more readable format, this will mean that:
- If it is thundering, the new `rainTime` will be between 12 000 and 24 000.
- If it is not thundering, the new `rainTime` will be between 12 000 and 180 000.

Which value somewhere in this range it becomes is determined by a random factor.  
After this the values that were changed will be reallocated to the corresponding fields.  
So now for the explanation of how this actually works. The way in which this works is a little confusing.  
`clearWeatherTime` isn’t used to determine how long the weather will stay clear outside of commands. Instead what happens is:  
The game starts and `clearWeatherTime`, `rainTime` and `thunderTime` are set to 0. `raining` and `thundering`  are set to false. Now, since everything is set to 0 it will immediately go to the part where it sets the timers, using the formulas we’ve just seen. In this case `rainTime` and `thunderTime` will be used to indicate how long it will **not** be raining or thundering. Both timers will count down, and only when they have reached 0 it will start raining or thundering, depending on which timer ran out. It will start this weather by flipping the boolean as we’ve seen before. After this a new time will be set for `rainTime` or `thunderTime`. This time, they’ll be used to indicate how long it will be raining or thundering. By constantly flipping the time `rainTime` and `thunderTime` represent, there’s no need to use `clearWeatherTime`. Thus we only have a value for this if we want to force the game to keep the weather clear for a certain amount of time no matter what.  

## Rain and thunder gradients
A whole separate part of the weather are rain and thunder gradients. These gradients are used to determine the `ambientLightLevel` and the colour of the sky. As you know the sky darkens when it starts to rain or thunder and brightens again when the weather becomes clear. Well these gradients will take care of that.

When the weather is clear, both gradients will be 0. The value for these gradients will always be between 0 and 1 inclusive. If it gets higher than 1 it will be set back to 1. If it gets lower than 0 it will be set back to 0. Each tick the following happens. If it is raining, each tick we’ll add 0.01 to the rain gradient. If it’s not raining we’ll subtract 0.01. This way when the sky will take 100 ticks or 5 seconds to change colour completely.

Now there is a `isRaining` and `isThundering` method, boh will determine if it’s raining or not via the gradient. These functions are used to determine the weather outside of the tick loop.

Rain will only begin to fall when the gradient is bigger than 0.2 and will only stop falling when it’s smaller than 0.2 again. In this case we’re speaking visually of course. Internally the properties are already set when the gradients start changing.

## Sleeping
First the time of day will be set to morning if a player has just finished sleeping. It will also reset the weather if the weather cycle is enabled. Resetting the weather set `rainTime` and `thunderTime` to 0 and `raining` and `thundering` to false.

## Daylight Cycle
If daylight cycle is enabled, the world time will be increased by one & the time of day will change.

## What about lightning strikes?
Lightning strikes don’t happen in this part of the tick but rather in the chunk part. See [Game Tick](https://techmcdocs.github.io/pages/GameTick/) for more info.
