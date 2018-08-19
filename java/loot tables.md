# Loot Tables

***

*Last updated: 1.12*

# Table of contents

### Generic info

1. [Intro](#intro)
2. [Errors](#errors)
3. [JSON Structure](#structure)

### Files

4. [Location](#files-location)
    1. [Namespaces](#files-location-namespaces)
5. [Referencing](#files-referencing)
6. [Replacing default tables](#default-tables)
    1. [List of default tables](#default-tables-list)
7. [Editing](#files-editing)

### Loot Construct

8. [General info](#construct-general)
9. [Luck](#construct-luck)
10. [Looted entity ("this")](#construct-this)
11. [Player context ("killer_player")](#construct-player)
12. [Damage source ("killer")](#construct-damage)

### Customizing tables

13. [Pools](#pools)
14. [Entries](#entries)
    1. ["item"](#entries-item)
    2. ["loot_table"](#entries-loot_table)
    3. ["empty"](#entries-empty)
15. [Rolls](#rolls)
16. [Bonus rolls](#bonus-rolls)
17. [Items](#items)
    1. [Weight](#items-weight)
    2. [Quality](#items-quality)
    3. [Functions](#items-functions)
        1. [List of functions](https://github.com/skylinerw/guides/blob/master/java/loot-tables/functions.md)
18. [Conditions](#conditions)
    1. [List of conditions](https://github.com/skylinerw/guides/blob/master/java/loot-tables/conditions.md)
19. [Duplicate functions & conditions](#duplicates)
    1. [Functions](#duplicates-functions)
    2. [Conditions](#duplicates-conditions)

### Usage

20. ["generic.luck", Luck, Unluck, Luck of the Sea](#usage-luck)
21. [NBT data](#usage-nbt)
    1. [Empty tables](#usage-nbt-empty)

### Conclusion

22. [Custom table example](#examples)
23. [Q&A](#qa)
24. [External links](#external-links)
25. [Conclusion](#conclusion)

# Generic info

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

1.9 has introduced an organized collection of loot tables that determines what type of items are acquired from chests, hoppers, dispensers, droppers, mob drops, chest/hopper minecarts, fishing, and advancement rewards. They allow a map/mod maker or server owner to modify what items are obtained as default loot, as well as add custom tables to be used alongside NBT data.

Loot tables are highly customizable, allowing numerous conditions with varying depths as well as functions to further modify the item(s) to provide. For command mechanisms, they will replace most loot-providing systems, such as those that use the `HandItems` and `ArmorItems` lists.

Loot tables use the [JSON format](http://json.org) to store the loot information in external files.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="errors">Errors</a>

When a loot table fails to provide an item due to an error with the loot table itself (whether it is related to the file or the JSON structure itself), the Game Output tab in the launcher will provide the error. If you are experiencing problems with your loot tables, you may be able to find the answer within the output.

To see the output, you must keep the launcher visible while playing. You can enable this by opening the Minecraft launcher, clicking on "Edit Profile", and changing the "Launcher Visibility" to *"Keep the launcher open"*:

![](http://i.imgur.com/U0QxX9t.png)

#### Common errors

> Couldn't find resource table minecraft:tablename

Ensure the loot table exists in the correct file location and has the `.json` extension.

> Expected value at line # column #

Your JSON structure is invalid. Double-check your syntax or use any JSON validator.

> Missing [key], expected to find a [type]

Your structure does not match what's required for loot tables. "key" is the name of the key you are missing, such as "type".

> Expected loot table to be a JsonObject, was "??"

This error may occur if your JSON is not structured as the game requires it, but can also occur if the file encoding is not correct (you should be saving files in UTF-8).

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="structure">JSON Structure</a>

The following is a list of all possible keys for loot tables. The wiki also includes the structure [here](http://minecraft.gamepedia.com/Loot_table#Tags).

```json
{
    "pools": [
        {
            "rolls": 1,
            "rolls": {
                "min": 1,
                "max": 1
            },
            "entries": [
                {
                    "type": "[item|loot_table|empty]",
                    "name": "[ID|LootTable]",
                    "weight": 1,
                    "quality": 1.0,
                    "functions": [
                        {
                            "function": "[FunctionID]",
                            "treasure": true,
                            "tag": "{display:{Name:\"Test\"}}",
                            "data": 1,
                            "data": {
                                "min": 1,
                                "max": 1
                            },
                            "damage": 1,
                            "damage": {
                                "min": 1,
                                "max": 1
                            },
                            "count": 1,
                            "count": {
                                "min": 1,
                                "max": 1
                            },
                            "levels": 1,
                            "levels": {
                                "min": 1,
                                "max": 1
                            },
                            "limit": 1,
                            "conditions": [
                                "see below"
                            ]
                        }
                    ],
                    "conditions": [
                        "see below"
                    ]
                }
            ],
            "conditions": [
                {
                    "condition": "[ConditionID]",
                    "chance": 1.0,
                    "looting_multiplier": 1.0,
                    "inverse": true,
                    "entity": "this",
                    "properties": {
                        "minecraft:on_fire": true
                    },
                    "scores": {
                        "[ObjectiveName]": 1,
                        "[ObjectiveName]": {
                            "min": 1,
                            "max": 1
                        }
                    }
                }
            ]
        }
    ]
}
```

# Files

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-location">Location</a>

Loot tables are saved within the world folder to be distributed with the world itself. More specifically, inside the `data/loot_tables` folder, which will **not** be created automatically. Here is an example structure within the world folder, where "New World" is the name of the world folder:

![](http://i.imgur.com/fnrQNcr.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-location-namespaces">Namespaces</a>

The folder that you create within the `loot_tables` folder will be referred to as the "namespace". This is what separates collections of loot tables, such as for different mods or maps. **File/folder names must all be lowercase to circumvent issues with differing operating system file structures.**

From the image example above, `skylinerwloot`, `mocreatures`, `anothermod`, and `minecraft` are the namespaces.

The `minecraft` namespace in particular should **only** be used to overwrite default loot tables, such as replacing natural zombie drops. Place any new loot tables in a new namespace and not within `minecraft`.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-referencing">Referencing</a>

When referencing a loot table, whether it's from within loot tables or in NBT data, you must follow the following format **excluding the `.json` extension**:

```
[namespace]:[filepath/to/table]
```

Example, targeting the file `anothermod/bears/grizzly.json`:

```
anothermod:bears/grizzly
```

By excluding the namespace, it will automatically assume it's meant to be `minecraft`:

```
entities/zombie
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="default-tables">Replacing default tables</a>

You can create a loot table within the `minecraft` namespace with the same name as the default loot tables. Anything that uses these default tables will then use your custom one, without the need to modify NBT.

For example, given the filepath `minecraft/entities/zombie.json`, the following loot table will cause all default zombies to drop 0-2 feathers when killed, instead of the default rotten flesh.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:feather",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:set_count",
                            "count": {
                                "min": 0,
                                "max": 2
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="default-tables-list">List of default tables</a>

The wiki has a description for each of the default loot tables [here](http://minecraft.gamepedia.com/Loot_table#List_of_loot_tables).

| Type  | Resource Location |
| ------------- | ------------- |
| Generic | minecraft:empty |
| Chest | minecraft:chests/abandoned_mineshaft<br>minecraft:chests/desert_pyramid<br>minecraft:chests/end_city_treasure<br>minecraft:chests/igloo_chest<br>minecraft:chests/jungle_temple<br>minecraft:chests/jungle_temple_dispenser<br>minecraft:chests/nether_bridge<br>minecraft:chests/simple_dungeon<br>minecraft:chests/spawn_bonus_chest<br>minecraft:chests/stronghold_corridor<br>minecraft:chests/stronghold_crossing<br>minecraft:chests/stronghold_library<br>minecraft:chests/village_blacksmith<br>minecraft:chests/woodland_mansion |
| Entity | minecraft:entities/bat<br>minecraft:entities/blaze<br>minecraft:entities/cave_spider<br>minecraft:entities/chicken<br>minecraft:entities/cow<br>minecraft:entities/creeper<br>minecraft:entities/donkey<br>minecraft:entities/elder_guardian<br>minecraft:entities/ender_dragon<br>minecraft:entities/enderman<br>minecraft:entities/endermite<br>minecraft:entities/evocation_illager<br>minecraft:entities/ghast<br>minecraft:entities/giant<br>minecraft:entities/guardian<br>minecraft:entities/horse<br>minecraft:entities/husk<br>minecraft:entities/iron_golem<br>minecraft:entities/llama<br>minecraft:entities/magma_cube<br>minecraft:entities/mule<br>minecraft:entities/mushroom_cow<br>minecraft:entities/ocelot<br>minecraft:entities/parrot<br>minecraft:entities/pig<br>minecraft:entities/polar_bear<br>minecraft:entities/rabbit<br>minecraft:entities/sheep<br>minecraft:entities/sheep/black<br>minecraft:entities/sheep/blue<br>minecraft:entities/sheep/brown<br>minecraft:entities/sheep/cyan<br>minecraft:entities/sheep/gray<br>minecraft:entities/sheep/green<br>minecraft:entities/sheep/light_blue<br>minecraft:entities/sheep/lime<br>minecraft:entities/sheep/magenta<br>minecraft:entities/sheep/orange<br>minecraft:entities/sheep/pink<br>minecraft:entities/sheep/purple<br>minecraft:entities/sheep/red<br>minecraft:entities/sheep/silver<br>minecraft:entities/sheep/white<br>minecraft:entities/sheep/yellow<br>minecraft:entities/shulker<br>minecraft:entities/silverfish<br>minecraft:entities/skeleton<br>minecraft:entities/skeleton_horse<br>minecraft:entities/slime<br>minecraft:entities/snowman<br>minecraft:entities/spider<br>minecraft:entities/squid<br>minecraft:entities/stray<br>minecraft:entities/vex<br>minecraft:entities/villager<br>minecraft:entities/vindication_illager<br>minecraft:entities/witch<br>minecraft:entities/wither_skeleton<br>minecraft:entities/wolf<br>minecraft:entities/zombie<br>minecraft:entities/zombie_horse<br>minecraft:entities/zombie_pigman<br>minecraft:entities/zombie_villager |
| Fishing | minecraft:gameplay/fishing<br>minecraft:gameplay/fishing/fish<br>minecraft:gameplay/fishing/junk<br>minecraft:gameplay/fishing/treasure |

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-editing">Editing</a>

You will need to use a UTF-8 compliant text editor when creating and saving loot tables that use unicode characters (such as the section symbol). By not encoding in UTF-8, special characters may not be saved correctly and Minecraft may fail to parse the data.

**File/folder names must all be lowercase to circumvent issues with differing operating system file structures.**

#### Windows Notepad

Windows Notepad is capable of saving as UTF-8, though will default to ANSI. Instructions for saving a `.json` in UTF-8:

1. Click *Save* or *Save As*.
2. Set *"Save as type"* to *"All files (*.*)"*.
3. Name your file and append the name with `.json`.
4. Set *"Encoding"* to *"UTF-8"*.
5. Save.

Image example:

![](http://i.imgur.com/K5aJeEc.png)

#### Notepad++

[Notepad++](https://notepad-plus-plus.org) is a great alternative to using plain Notepad. Instructions for saving a `.json` in UTF-8:

1. Change the encoding to either *"UTF-8"* or *"UTF-8 without BOM"* under the *"Encoding"* menu. See image below.
2. Click *Save* or *Save As*.
3. Set *"Save as type"* to *"All files (*.*)"*.
4. Name your file and append the name with `.json`.
5. Save.

Image example:

![](http://i.imgur.com/NLut7F1.png)

#### Atom

[Atom](https://atom.io) has multi-platform support. Instructions for saving a `.json` in UTF-8:

1. Change the encoding to *"UTF-8"* under the *"Select Encoding"* menu. See image below.
2. Click *Save* or *Save As*.
3. Set *"Save as type"* to *"All files (*.*)"*.
4. Name your file and append the name with `.json`.
5. Save.

Image example:

![](http://skylinerw.com/images/advancements/editing_3.png)

# Loot construct

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="construct-general">General info</a>

When a loot table is accessed, some specific information is passed to it for the parser to work with. For example, a killed entity will be stored and passed to the loot table to use for features that look for a "this" entity (such as the `entity_properties` condition).

Not all of the necessary information is stored. Depending on the feature using the loot table, a "damage source" is not relevant (such as opening a chest). The following will describe the information that can be stored, when it actually is stored, and how it is used and can be used.

**All cases when info is stored:**

1. When a mob dies
2. When minecart chests are opened
3. When minecart hoppers are opened
4. When chests are opened
5. When hoppers are opened
6. When dispensers are opened
7. When droppers are opened
8. When fishing

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="construct-luck">Luck</a>

See also: [bonus_rolls](#bonus-rolls), [quality](#quality), [Luck usage](#usage-luck)

"Luck" is stored as a single float value, usually being the final `generic.luck` attribute value of the player but will also be modified by the player's "Luck of the Sea" enchantment level for fishing.

Note that while this value is obtained from a relevant player, that player does not necessarily get stored as [player context](#construct-player).

**Hard-coded usage:**

*None*

**Loot table usage:**

1. The [`bonus_rolls`](#bonus-rolls) parameter for pools.
2. The [`quality`](#quality) parameter for entries.

**When it is stored:**

1. When a mob dies
2. When minecart chests are opened
3. When minecart hoppers are opened
4. When chests are opened
5. When hoppers are opened
6. When dispensers are opened
7. When droppers are opened
8. When fishing

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="construct-this">Looted entity ("this")</a>

Stores the entity class that was looted.

**Hard-coded usage:**

*None*

**Loot table usage:**

1. Accessed when using the "this" context for conditions.

**When it is stored:**

1. When a mob dies

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="construct-player">Player context ("killer_player")</a>

Stores the player class, intended to signify that the player had been the one who killed the looted entity. This does not mean the player was the one who dealt the final blow. Technically it does not even have to mean the player did any killing at all, as there is a possible player for the game to potentially store when opening a chest or fishing, but is only stored in cases where a mob dies.

**Hard-coded usage:**

1. The "killed_by_player" condition.

**Loot table usage:**

1. Accessed when using the "killer_player" context for conditions.

**When it is stored:**

1. When a mob dies (as long as the player had struck the mob within 3 seconds before death, or as long as a zombie pigman is angry at the player)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="construct-damage">Damage source ("killer")</a>

Stores information concerning the final blow to the killed entity. The killing entity that dealt the final blow, if applicable, is stored alongside this information.

However, only the killing entity is ever accessed and the rest of the damage information that is stored (such as the type of damage, like "fall" or "thorns") is not yet used. Even if there was no killing entity, the damage source will still be stored but is also still unused.

**Hard-coded usage:**

1. The "looting_enchant" condition, obtaining the killing entity info.
2. The "random_chance_with_looting" condition, obtaining the killing entity info.

**Loot table usage:**

1. The killing entity info is accessed when using the "killer" context for conditions.

**When it is stored:**

1. When a mob dies.

# Customizing tables

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="pools">Pools</a>

The collection of different items that can be dropped, dependent on conditions or other factors, stored within the `pools` list. Every record within the pool will be accessed, each of which has its own set of entries, conditions, and rolls. The following has two different records within the pool; the first record will always result in stone, while the second record will be either another stone or a stick. The end result is either 2 stone or 1 stone and 1 stick.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                }
            ]
        },
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stick",
                    "weight": 1
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entries">Entries</a>

Each pool requires a declaration of entries via the `entries` list. Each entry will then **require** stating what type of entry it is with the `type` string key. The accepted types are "item", "loot_table", and "empty".

The following will provide either an item, a nested loot table, or nothing.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:feather",
                    "weight": 1
                },
                {
                    "type": "loot_table",
                    "name": "minecraft:entities/chicken",
                    "weight": 1
                },
                {
                    "type": "empty",
                    "weight": 1
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entries-item">`item`</a>

States that the entry will be an actual item. The `name` string key is **required** and defines the item ID to be used. This particular type supports the use of [functions](#items-functions). See the [Items](#items) section for extended usage of this type.

The following will provide either a stone or a feather.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:feather",
                    "weight": 1
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entries-loot_table">`loot_table`</a>

States that the entry will be another loot table. This simplifies loot tables that uses the same data as another. The `name` string key is **required** and defines which loot table is to be used. Does **not** support [functions](#items-functions).

Note that the other loot table's provided item is not affected by this loot table. If the other loot table has 3 rolls while this one has 1, the other loot table will cycle through all 3 of its own rolls.

A loot table may not point to itself in any manner. If the root loot table points to a *second* loot table that selects the root in a loop, the loot table will not function and no item will be provided.

The following will provide either an item from its own loot table, or provide item(s) from another loot table.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:feather",
                    "weight": 1
                },
                {
                    "type": "loot_table",
                    "name": "minecraft:entities/zombie",
                    "weight": 1
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entries-empty">`empty`</a>

States that the entry will be nothing. No item will be provided. Does **not** support `functions` and the `name` string key cannot be used. While `conditions` may be used, they are essentially useless.

The following will provide either nothing or a stone.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "empty",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rolls">Rolls</a>

A roll is the number of times the loot table will pick an item from its pool. It can either be defined as a `roll` integer key or a `roll` compound with `min` and `max` keys defined. In either case, a roll amount is **required**.

The following will provide exactly 3 results from the list of items.

```json
{
    "pools": [
        {
            "rolls": 3,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stick",
                    "weight": 1
                }
            ]
        }
    ]
}
```

The following will provide between 1 and 5 results from the list of items.

```json
{
    "pools": [
        {
            "rolls": {
                "min": 1,
                "max": 5
            },
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stick",
                    "weight": 1
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="bonus-rolls">Bonus rolls</a>

See also: [quality](#quality), [luck usage](#usage-luck)

The player's `generic.luck` attribute can optionally modify the number of total `rolls` a pool will perform. The number of bonus rolls is specified with the `bonus_rolls` integer/compound key. If compound, it defines a minimum and maximum range with the "min" and "max" integers.

The formula is:

```
rolls + (bonus_rolls * generic.luck)
```

As such, if the bonus roll is negative while luck is positive, the number of total rolls provided is **decreased**. For example, with 1 roll, -1 bonus rolls, and a luck value of 1, the result is 0 rolls from that pool:

```
1 + (-1 * 1) = 0
```

The following will always provide 2 stone (due to 2 initial rolls), and will also provide 1 extra stone for each point of "luck" the player has. If the player has 0 luck, they will be given 2 stone. If they have 1 luck, they will be given 3 stone.

```json
{
    "pools": [
        {
            "rolls": 2,
            "bonus_rolls":1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                }
            ]
        }
    ]
}
```

The following will provide between 2-6 extra stone.

```json
{
    "pools": [
        {
            "rolls": 2,
            "bonus_rolls": {
                "min": 2,
                "max": 6
            },
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="items">Items</a>

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="items-weight">Weight</a>

A weight determines the chances of an item being picked in comparison to the weights of other items. If all items have the same weight, they all have the same chance of being picked. A weight is defined by the `weight` integer key and is **optional**. If not specified, the value defaults to 1. The following will provide 1 stone at *half* the rate as 1 stick.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stick",
                    "weight": 2
                }
            ]
        }
    ]
}
```

To get an idea of how weights are used, imagine the instance of each item being multiplied by its weight. Using the above table, the following is a visual representation in which there are 2 sticks (weight of 2) and 1 stone (weight of 1). Since there are 2 sticks, they have a higher chance (exactly double) of being chosen compared to stone.

```
minecraft:stone, minecraft:stick, minecraft:stick
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="items-quality">Quality</a>

See also: [`bonus_rolls`](#bonus-rolls), [luck usage](#usage-luck)

Optionally changes the `weight` of the entry based on the killing/opening/fishing player's `generic.luck` attribute value. The attribute can be modified by either worn gear with the `AttributeModifiers` list or by giving the player the `Luck` potion effect.

The formula is:

```
weight + (quality * generic.luck)
```

The following will provide either stone or diamond at equal rates while the player does **not** have any Luck, but if the player has a Luck value of 2, the diamond will have its `weight` changed to 7.

```json
{
    "pools": [
        {
            "rolls": 20,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:diamond",
                    "weight": 1,
                    "quality": 3
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="items-functions">Functions</a>

Functions are **optional** operations to perform on the item, such as providing it a count between two values, giving it NBT data or a Damage value, as well as enchanting it using a random enchantment. The `functions` list will hold multiple compounds, while the `function` string key states which function is to be used. Individual functions may ask for more data, which is covered in the next section.

The IDs for functions will have the resource namespace default to "minecraft" (e.g. `minecraft:set_attributes`) when not specified. If using mods, the namespace will be whatever the mod has implemented. Note that you cannot create your own functions without modding.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:diamond_sword",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "set_damage",
                            "damage": 0.6
                        },
                        {
                            "function": "minecraft:enchant_with_levels",
                            "levels": {
                                "min": 20,
                                "max": 30
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) List of functions

See [here](https://github.com/skylinerw/guides/blob/master/java/loot-tables/functions.md) for a list of functions.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conditions">Conditions</a>

Conditions are a list of requirements that must be met before either a pool can be used, an individual entry can be selected, or before a function can be run. All conditions are stored within the `conditions` list, with the `condition` string key stating which internal condition function is used. Conditions may be applied to a pool to prevent the pool itself from being used, applied to an individual entry to cause the entry to be ignored before rolling, or within [`functions`](#items-functions) to prevent a function from working.

The IDs for conditions will have the resource namespace default to "minecraft" (e.g. `minecraft:random_chance`) when not specified. If using mods, the namespace will be whatever the mod has implemented. Note that you cannot create your own conditions without modding.

Each condition runs one at a time. If any one condition in the list fails, the remainder in the same list will be ignored.

**Pool conditions**

The following only provides items within the pool if the condition is met. Both the stone and stick will only be provided when killed by a player, otherwise nothing is provided.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stick",
                    "weight": 1
                }
            ],
            "conditions": [
                {
                    "condition": "killed_by_player"
                }
            ]
        }
    ]
}
```

**Entry conditions**

The following only provides the individual entry if the condition is met. The stone will always drop, but the stick will only drop (at a 50% rate due to the weight) when killed by a player.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stick",
                    "weight": 1,
                    "conditions": [
                        {
                            "condition": "minecraft:killed_by_player"
                        }
                    ]
                }
            ]
        }
    ]
}
```

Conditions for all entries are tested *before* randomly picking an entry from a pool. If a condition for an entry fails, the entry will not be selected whatsoever by the pool. This means the weight can be affected unexpectedly, since entries that are thrown out due to conditions will no longer be contributing to the total weight.

For example, with the following table, the stone and cactus will each have a 1% chance of dropping, while the stick has a 98% chance. The total weight, provided the condition for the stick succeeds, is 100. However, if the condition for the stick fails, the stone and cactus will each have a 50% chance of dropping instead. The total weight will instead be 2.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:cactus",
                    "weight": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stick",
                    "weight": 98,
                    "conditions": [
                        {
                            "condition": "minecraft:killed_by_player"
                        }
                    ]
                }
            ]
        }
    ]
}
```

**Function conditions**

The following will only run the individual function if a condition was met. The following will run the `furnace_smelt` function only if the mob was killed by a player. If a player kills the mob, smooth stone will drop. Otherwise, cobblestone will drop.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:cobblestone",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:furnace_smelt",
                            "conditions": [
                                {
                                    "condition": "minecraft:killed_by_player"
                                }
                            ]
                        }
                    ]
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) List of conditions

See [here](https://github.com/skylinerw/guides/blob/master/java/loot-tables/conditions.md) for a list of conditions.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="duplicates">Duplicate functions & conditions</a>

Multiple of the same functions and conditions can be applied, though not all will work or are useful.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="duplicates-functions">Functions</a>

#### `set_count`

Only the last `set_count` function specified will be used.

#### `set_data`

Only the last `set_data` function specified will be used.

#### `set_damage`

Only the last `set_damage` function specified will be used.

#### `set_nbt`

NBT data will merge. If duplicate tags are declared, the last `set_nbt` function specified will take precedence. For example, the following would provide an item that contains the lore from the first function, but the second function overwrites the display name from the first.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:cobblestone",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:set_nbt",
                            "tag": "{display:{Name:\"Function 1\",Lore:[\"text\"]}}"
                        },
                        {
                            "function": "minecraft:set_nbt",
                            "tag": "{display:{Name:\"Function 2\"}}"
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### `set_attributes`

Attributes will be appended. Note that if `id` is duplicated, the game will not account for it. **Do not use multiple of the same UUID**.

#### `enchant_randomly`

Enchantments will be appended, rather than merged. Note that if the same enchantment is picked twice, they will not merge together and will instead be listed one after the other (and does not have to have the same level).

#### `enchant_with_levels`

Enchantments will be appended, rather than merged. Note that if the same enchantment is picked twice, they will not merge together and will instead be listed one after the other (and does not have to have the same level).

#### `looting_enchant`

Counts will be merged. For example, the following provides 42 stone with Looting I; 1 for initial, an extra 30 for the first function, and an extra 11 for the second function.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:looting_enchant",
                            "count": 30
                        },
                        {
                            "function": "minecraft:looting_enchant",
                            "count": 11
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### `furnace_smelt`

Each function will run one at a time, processing the item before moving on to the next. For example, in the following, the first function will turn the cactus into green dye, and the second function will fail because green dye has no smelting recipe.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:cactus",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:furnace_smelt"
                        },
                        {
                            "function": "minecraft:furnace_smelt"
                        }
                    ]
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="duplicates-conditions">Conditions</a>

#### <a name="duplicates-conditions-random_chance">`random_chance`</a>

Each condition runs one at a time (so long as no conditions fail), which can greatly change the rate of selection. For example, the following has a 50% chance for the first function, and if the first function succeeded, a 25% chance for the second function.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone_sword",
                    "weight": 1,
                    "conditions": [
                        {
                            "condition": "minecraft:random_chance",
                            "chance": 0.5
                        },
                        {
                            "condition": "minecraft:random_chance",
                            "chance": 0.25
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### `random_chance_with_looting`

Each condition runs one at a time (so long as no conditions fail), which can greatly change the rate of selection. See [`random_chance`](#duplicates-conditions-random_chance), which essentially functions the same way (albeit without a looting multiplier).

#### `killed_by_player`

Each condition runs one at a time (so long as no conditions fail). However, since the only option is whether or not the mob was killed by a player, it would be pointless to have multiple of these conditions at the same depth.

#### `entity_properties`

Each condition runs one at a time (so long as no conditions fail). It would be pointless to have multiple of these conditions if `entity` remained the same. However, it will function well for different targets. For example, the following would provide stone so long as both the mob and the killer were on fire.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:cobblestone",
                    "weight": 1,
                    "conditions": [
                        {
                            "condition": "minecraft:entity_properties",
                            "entity": "this",
                            "properties": {
                                "on_fire": true
                            }
                        },
                        {
                            "condition": "minecraft:entity_properties",
                            "entity": "killer",
                            "properties": {
                                "on_fire": true
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

#### `entity_scores`

Each condition runs one at a time (so long as no conditions fail). It would be pointless to have multiple of these conditions if `entity` remained the same. However, it will function well for different targets. For example, the following would provide stone so long as the killed mob had an "ObjA" score of 1, and the killer had an "ObjA" score between 1 and 2.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:cobblestone",
                    "weight": 1,
                    "conditions": [
                        {
                            "condition": "minecraft:entity_scores",
                            "entity": "this",
                            "scores": {
                                "ObjA": 1
                            }
                        },
                        {
                            "condition": "minecraft:entity_scores",
                            "entity": "killer",
                            "scores": {
                                "ObjA": {
                                    "min": 1,
                                    "max": 2
                                }
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

# Usage

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="usage-luck">generic.luck, Luck, Unluck, Luck of the Sea</a>

There are numerous features in-game that refer to "luck", such as the attribute, potion effect, and enchantment. Luck is specifically used with loot tables in order to modify potential loot chances. See [`bonus_rolls`](#bonus-rolls) and [`quality`](#quality) for implementing into loot tables.

**In default vanilla, the only thing that "luck" affects is *fishing*.** It is a common misconception that "luck" does anything else. It does **not** affect mob or chest loot because the default loot tables for them do **not** implement the [`bonus_rolls`](#bonus-rolls) or [`quality`](#quality) tags. Custom loot tables would need to be created to implement them. As well, the "Lure" enchantment no longer affects loot chances.

The `generic.luck` attribute is what controls the player's current luck value, with a base value of 0. The player can be given items that modify this value using all of the features of attribute modifiers. For example, the following provides the player with an iron ingot that, when held in the off-hand, increases the player's `generic.luck` attribute by 2.0:

```
/give @p minecraft:iron_ingot 1 0 {AttributeModifiers:[{AttributeName:"generic.luck",Name:"More Luck",Amount:2.0,Operation:0,Slot:"offhand",UUIDLeast:1l,UUIDMost:1l}]}
```

The "Luck" and "Unluck" potion effects will apply modifiers to the player's `generic.luck` attribute, adding 1.0 per effect level of "Luck" or removing 1.0 per effect level of "Unluck". For example, the following gives the player "Luck" 10 and "Unluck" 4, which has a final resulting `generic.luck` value of 6:

```
/effect @p minecraft:luck 30 9
/effect @p minecraft:unluck 30 3
```

The "Luck of the Sea" enchantment does not directly add an attribute modifier like the "Luck" or "Unluck" effects. Instead, the level of the enchantment is added onto the player's *final* `generic.luck` value (but **only** when obtaining fishing loot). As such, the enchantment supersedes other modifiers placed on the player. If the player had a modifier that reduced their attribute value by 100% (which occurs after any addition modifiers), their enchantment would still work because it is adding the enchantment level onto the final base of 0.0.

For example, the following provides the player with a "multiply total" attribute modifier, reducing their `generic.luck` by 100%, as well as a fishing rod with a high "Luck of the Sea" enchantment. The result is the player being guaranteed to fish treasure items because the enchantment value is added after obtaining the final attribute value (0.0 attribute + 85 enchantment = 85 "luck", the lowest value needed to guarantee treasure from fishing):

```
/give @p minecraft:iron_helmet 1 0 {AttributeModifiers:[{AttributeName:"generic.luck",Name:"100% reduction",Amount:-1.0,Operation:2,Slot:"head",UUIDLeast:1l,UUIDMost:1l}]}

/give @p minecraft:fishing_rod 1 0 {ench:[{id:61s,lvl:85s}]}
```

After all of these values are totalled up, the resulting value is what loot tables will use when modifying the [`bonus_rolls`](#bonus-rolls) and [`quality`](#quality) tags.

For example, the following gives the player an iron helmet that, when worn, increases the player's "generic.luck" value by 100% (doubling their value), a "Luck" effect of 40 (which provides a modifier of +40.0), and a fishing rod with a "Luck of the Sea 5" enchantment. The result is a final attribute value of 80.0 (40 * 2), with 5 added on from the enchantment, finally resulting in 85 (guaranteeing treasure while fishing):

```
/give @p minecraft:iron_helmet 1 0 {AttributeModifiers:[{AttributeName:"generic.luck",Name:"100% More",Amount:1.0,Operation:2,Slot:"head",UUIDLeast:1l,UUIDMost:1l}]}

/effect @p minecraft:luck 30 39

/give @p minecraft:fishing_rod 1 0 {ench:[{id:61s,lvl:5s}]}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="usage-nbt">NBT data</a>

#### `LootTable` (string)

The `LootTable` tag holds the filepath to the desired loot table. This tag's usage is restricted to the following: chests, hoppers, dispensers, droppers, minecart chests, minecart hoppers.

The following will create a chest that will populate with items from the "minecraft:chests/simple_dungeon" loot table:

```
/setblock ~ ~1 ~ minecraft:chest 0 false {LootTable:"minecraft:chests/simple_dungeon"}
```

#### `LootTableSeed` (long)

Instead of being random every single time, inventory population can be deterministic based on seed. A default survival world will use this to always generate the same loot whenever a new world with the same seed is created. Value must not be 0, otherwise a new seed will be chosen randomly. This tag's usage is restricted to the following: chests, hoppers, dispensers, droppers, minecart chests, minecart hoppers.

```
/setblock ~ ~1 ~ minecraft:chest 0 false {LootTable:"minecraft:chests/simple_dungeon",LootTableSeed:123L}
```

#### `DeathLootTable` (string)

Mobs use this tag to determine what loot table is used when killed. Note that the `HandItems/ArmorItems` data for mobs is unrelated to loot tables and can drop separate items.

```
/summon Creeper ~ ~1 ~ {DeathLootTable:"minecraft:chests/simple_dungeon"}
```

#### `DeathLootTableSeed` (long)

Same as `LootTableSeed`, except for mobs.

```
/summon Creeper ~ ~1 ~ {DeathLootTable:"minecraft:chests/simple_dungeon",DeathLootTableSeed:123L}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="usage-nbt-empty">Empty tables</a>

An empty loot table may be declared by using `minecraft:empty`. This will prevent any default loot from generating, provided that this table has not been overwritten with a custom table.

```
/summon Creeper ~ ~1 ~ {DeathLootTable:"minecraft:empty"}
```

# Conclusion

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="examples">Custom table example</a>

Since the `LootTable/DeathLootTable` tags states the path to the desired loot table within the target folder, rather than using hard-coded tables, you can add your own table to the mix.

The following is an example of a loot table named `custom_table_a.json` within the "entities" folder, inside the "customloot" namespace. It has a 1% chance of providing TNT, so long as it was on fire and killed by a player, otherwise it provides nothing.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:tnt",
                    "weight": 1,
                    "conditions": [
                        {
                            "condition": "minecraft:random_chance",
                            "chance": 0.01
                        },
                        {
                            "condition": "minecraft:killed_by_player"
                        },
                        {
                            "condition": "minecraft:entity_properties",
                            "entity": "this",
                            "properties": {
                                "minecraft:on_fire": true
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

And then using that in-game is the same as before, replacing the name with the name given to the JSON file. The following summons a creeper that, when killed, has a 1% chance to drop TNT when killed by a player and on fire.

```
/summon Creeper ~ ~1 ~ {DeathLootTable:"customloot:entities/custom_table_a"}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="qa">Q&A</a>

Q&A last updated: 16w02a.

> Is there an easy way to reload loot tables in-game if I've made changes?

Yes, hold F3 and press T to reload all resources.

> Do custom loot tables work on servers?

Yes, the file structure is exactly the same. A singleplayer world with custom loot tables can be copied to a multiplayer server with no need to change the tables.

> Are loot tables a part of resource packs?

No. Loot tables (and structures) are server-side, while resource packs are client-side. The difference is the client themselves being unable to change server resources. Loot tables and structures are not packaged with a resource pack.

> Where can I find the default loot tables?

You will need to extract "assets/loot_tables" from: /.minecraft/versions/15w49a.jar (or higher)

> There is no "loot_tables" folder within my world save, does it get created automatically?

It does not; the folder must be created manually.

> Why should I not use the "minecraft" namespace for new tables?

It is bad practice to use the default namespace for tables that are not default. There is also the risk of the filename you create being used in the future, resulting in unexpected overwriting of default resources. You should always put new loot tables into a separate namespace.

> Can I use target selectors for "entity"?

No, we are currently restricted to "this", "killer", and "killer_player".

> Can I use fake player scores for "entity_scores"?

No, we are currently restricted to "this", "killer", and "killer_player". You can use /scoreboard to duplicate the desired fake player score onto the killer or victim. You'll want to apply the score to as few targets as possible to save on scoreboard.dat bloating. For example, there will likely be fewer players compared to mobs in a CTM map, so you'll copy the fake player scores to the players ("killer_player") rather than to the killed mob ("this").

> Do I have to include "minecraft" before item IDs, condition IDs, and function IDs in loot tables?

No, the namespaces will default to "minecraft" when not defined.

> Accented characters/section symbol does not show up correctly in-game. What happened?

Your JSON file was not encoded in UTF-8. See [Editing](#files-editing) for info.

> What does "luck" do for default vanilla Minecraft?

The **only** thing luck affects in default vanilla is fishing loot tables. It is a common misconception that it affects mob drops or chest loot, but that is incorrect because default loot tables do not incorporate luck into anything except the fishing loot tables.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="external-links">External links</a>

- [Generic JSON Validator](http://jsonlint.com)
- [Minecraft Wiki: Loot table](http://minecraft.gamepedia.com/Loot_table)
- [Javascript loot table generator](https://jsfiddle.net/MrPingouin/125mx5r5/embedded/result) by [MrPingouin1](http://www.minecraftforum.net/members/MrPingouin1)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conclusion">Conclusion</a>

If there is information that needs to be added, corrected, or clarified, please create a new issue.
