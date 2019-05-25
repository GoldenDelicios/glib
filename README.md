# glib
Minecraft datapack: A library for functions and tags

## How to use:
* Drop the datapack into your world's datapacks folder (`.minecraft/saves/<world>/datapacks/` for singleplayer).  
* Type `/reload` or restart your world to enable the datapack.  

## What is GLib?
* This is a datapack that provides common functions and tags for use in other datapacks.
* There are a few goals that this datapack intends to accomplish:
  * Minimizing static lag caused by datapacks with similar functions by providing access to common functions and taking advantage of function tags.
  * Compatibility across different versions of the game. This isn't possible to guarantee as the game is always changing things relevant to commands or datapacks, but mainly this datapack will avoid handling things to do with items or blocks or their tags.
  * Compatibility with different versions of itself, to allow the option of datapacks to come prepackaged with GLib in the future, without needing the user to download GLib separately.
* Because of the last goal, I consider GLib to still be in alpha as I take some time to experiment with optimization and future potential uses. Until then, any developers who wish to use GLib should probably not have GLib prepackaged and instead have their user download it separately.

## Early documentation:

### Functions

#### `glib:clouds/eternalclouds`
* Resets all loaded area effect clouds tagged "EternalCloud" to `{Age:0}`.
* You may wish to use this if your datapack uses clouds as permanent entity markers.
* It is recommended to tag this function as `#glib:min` if you wish to use it.

#### `glib:clouds/spawnpoint`
* Periodically summons a cloud tagged "SpawnPoint" at the world spawn in the Overworld.
  * This cloud lasts 2000 ticks.
  * By resummoning it periodically, you can detect changes to world spawn point made by an operator or command.
* This function needs to be run as/at the Server to properly mark spawn.
* You may wish to use this if your datapack needs to use spawn as a reference point in functions not run as/at the Server.
* It is recommended to either run this using `/schedule` or to tag this function as `#glib:min` if you wish to use it.

#### `glib:math/randomize`
* Generates a random number between a provided "Min" and "Max".
* Stores it as the executor's score as well as in "FinalValue", in case the executor is not an entity (i.e. Server, command block, etc).
* e.g. This function will generate a random number between 1 and 6 for each player. It will also print the final dice rolled (in this case, the dice rolled by the furthest player) as "FinalValue" was overwritten for each player the randomizer was run.
```
scoreboard players set Min Randomizer 1
scoreboard players set Max Randomizer 6
execute as @a[sort=nearest] run function glib:math/randomize
tellraw @a [{"text":"You rolled a dice and got "},{"score":{"name":"@s","objective":"Randomizer"}}]
tellraw @a [{"text":"The last dice rolled was a "},{"score":{"name":"FinalValue","objective":"Randomizer"}}]
```
* It is recommended to only run this function as needed.

#### `glib:schedule/tick`
* Manages the schedule system, along with the other functions in the same folder.
* Runs `#glib:day` every 1,728,000 ticks, `#glib:hour` every 72,000 ticks, `#glib:min` every 1200 ticks, `#glib:sec` every 20 ticks, and `#glib:tick` every tick, in that order.
  * To clarify, within the same tick, `#glib:day` will run before `#glib:hour`, which will run before `#glib:min`, etc.
  * `#glib:tick` is used for any functions that run every tick but need to ensure that they are run *after* day, hour, min, and/or sec functions are run.
* Functions that use any of the tags above require this function to be tagged as `#minecraft:tick`.

#### `glib:tools/input`
* Detects carrot-on-a-stick input and runs `#glib:tools`, then resets input *after* all the functions are run.
* If you use this function, do not reset the player's "ToolInput" score, as other functions may need to use it.
* Additionally, as the developer you should ensure that your custom tool is uniquely identified, possibly with a custom name, RepairCost, or other item tag.
* It is recommended to tag this function as `#minecraft:tick` if you wish to use it.

### Function tags
#### `#glib:day`
* Runs its functions every real-life day (1,728,000 ticks).
* Requires `glib:schedule/tick` to be tagged as `#minecraft:tick`.

#### `#glib:hour`
* Runs its functions every real-life hour (72,000 ticks).
* Always runs after `#glib:day`, if applicable.
* Requires `glib:schedule/tick` to be tagged as `#minecraft:tick`.

#### `#glib:min`
* Runs its functions every real-life minute (1200 ticks).
* Always runs after `#glib:hour`, if applicable.
* Requires `glib:schedule/tick` to be tagged as `#minecraft:tick`.

#### `#glib:sec`
* Runs its functions every 20 ticks.
* Always runs after `#glib:min`, if applicable.
* Requires `glib:schedule/tick` to be tagged as `#minecraft:tick`.

#### `#glib:tick`
* Runs its functions every game tick, after all the functions above.
* Requires `glib:schedule/tick` to be tagged as `#minecraft:tick`.

#### `#glib:tools`
* Runs its functions whenever a player right-clicks with a carrot-on-a-stick.
* Requires `glib:tools/input` to be tagged as `#minecraft:tick`.
