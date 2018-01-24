# Advancements

*Last updated: 18w03b*

***

# Table of contents

### Generic info

1. [Intro](#intro)
2. [Errors](#errors)

### Files

3. [Location](#files-location)
    1. [Namespaces](#files-location-namespaces)
4. [Referencing](#files-referencing)
5. [Replacing default advancements](#default-advancements)
    1. [List of default advancements](https://minecraft.gamepedia.com/Advancements#List_of_advancements)
6. [Editing](#files-editing)

### Shared data structures

7. [JSON structure](#structure)
8. [Shared: range](#generic-range)
9. [Shared: location object](#generic-location)
10. [Shared: distance object](#generic-distance)
11. [Shared: status effects object](#generic-effects)
12. [Shared: item object](#generic-item)
13. [Shared: entity object](#generic-entity)
14. [Shared: block object](#generic-block)
15. [Shared: damage object](#generic-damage)
16. [Shared: damage flags object](#generic-damage-flags)
17. [Shared: death object](#generic-death)

### Customizing advancements

18. [Criteria](#criteria)
    1. [List of triggers](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md)
    2. [Processing order of triggers](#criteria-order)
19. [Requirements](#requirements)
20. [Display](#display)
    1. [Title, description, & icon](#display-title-desc-icon)
    2. [Background](#display-background)
    3. [Frame](#display-frame)
    4. [Show toast](#display-toast)
    5. [Announce to chat](#display-announce)
    6. [Hidden](#display-hidden)
21. [Rewards](#rewards)
    1. [`recipes`](#rewards-recipes)
    2. [`loot`](#rewards-loot)
    3. [`experience`](#rewards-experience)
    4. [`function`](#rewards-function)
22. [Tree display](#tree)
    1. [Root](#tree-root)
    2. [Branches](#tree-branches)

### Commands

23. [Command: `/advancement`](#command-advancement)

### Conclusion

24. [Q&A](#qa)
25. [External links](#external-links)
26. [Conclusion](#conclusion)

# Generic info

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

Starting in 1.12, achievements, now referred to as "advancements", have become data-driven via modifiable JSON files rather than hard-coded. This allows map makers, modders, and server owners to modify and add advancements to their liking.

Advancements have a multitude of options, ranging from triggers based on the player's inventory to what biome they're in. Rewards can optionally be given, such as unlocking a recipe, providing an item, or providing experience. A new command, `/advancement`, has been introduced to supplement the files.

Advancements use the [JSON format](http://json.org) to store the advancement information in external files.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="errors">Errors</a>

When an advancement fails for whatever reason, you can find failure messages in the Game Output window in the log files.

### Game Output window

To open the Game Output window when launching the game, you must enable it in the "Settings" tab of the launcher.

![](http://skylinerw.com/images/advancements/errors_output_1.png)

This window will show the error and stacktrace explaining the issue.

![](http://skylinerw.com/images/advancements/errors_output_2.png)

### Log files

If you no longer have the Game Output window open, you can check the output log. You can quickly access the Minecraft program files in the launcher by navigating to **Launch Options -> + Add New**, and clicking the "Go to folder" button:

![](http://skylinerw.com/images/advancements/errors_output_3.png)

From there, navigate to the `/logs/latest.log` file, which will contain the stacktrace. In this case, the error is stating that the advancement has a display, but is missing a description:

```
[07:02:59] [Server thread/ERROR]: Parsing error loading custom advancement custom:example
com.google.gson.JsonSyntaxException: Missing description
    at qe.a(SourceFile:447) ~[17w15a.jar:?]
    at r.a(SourceFile:66) ~[17w15a.jar:?]
```

# Files

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-location">File location</a>

Advancements are saved as a datapack in the world folder to be distributed with the world itself, inside the `/datapacks/` folder. Here is an example structure within the world folder, where "New World" is the name of the world folder:

![](http://skylinerw.com/images/advancements/files_2.png)

For more information on setting up a data pack (including the creation of the required `pack.meta` file), see [here](https://minecraft.gamepedia.com/Data_pack).

### <a name="files-location-namespaces">Namespaces</a>

The root folder that you create within the `/New World/datapacks/<datapack name>/data/` folder will be referred to as the "namespace". This is what separates collections of advancements, such as for different mods or maps. **File/folder names must all be lowercase to circumvent issues with differing operating system file structures.**

From the image example above, `skylinerwadvancements`, `anothermod`, and `minecraft` are the namespaces.

The `minecraft` namespace in particular should **only** be used to overwrite default advancements, such as with the intent to prevent them from working. Place any new advancements in a new namespace and **not** within `minecraft`.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-referencing">Referencing</a>

When referencing an advancement, whether it's from a [parent](#tree-root) or in a [command](#command-advancement), you must follow the following format **excluding the `.json` extension**:

```
[namespace]:[filepath/to/advancement]
```

Example, targeting the file `/New World/datapacks/<datapack name>/data/anothermod/advancements/challenges/kill_creepers.json`:

```
anothermod:challenges/kill_creepers
```

By excluding the namespace, it will automatically assume it's meant to be `minecraft`:

```
story/elytra = minecraft:story/elytra
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="default-advancements">Replacing default advancements</a>

You can create an advancement within the `minecraft` namespace with the same name as a default advancement. Anything that uses the default advancement will then use your custom one.

For example, given the filepath `/New World/datapacks/<datapack name>/minecraft/advancements/story/elytra.json`, the following advancement will replace the default advancement that triggers by having any elytra in the inventory, and instead triggers by having an elytra slightly damaged in the inventory (which can trigger when starting to fly):

```json
{
    "display": {
        "icon": {
            "item": "minecraft:elytra"
        },
        "title": "Learn to Fly"
    },
    "parent": "minecraft:story/enter_end_gateway",
    "criteria": {
        "elytra": {
            "trigger": "minecraft:inventory_changed",
            "conditions": {
                "items": [
                    {
                        "item": "minecraft:elytra",
                        "durability": 431
                    }
                ]
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-editing">Editing</a>

You will need to use a UTF-8 compliant text editor when creating and saving loot tables that use unicode characters (such as the section symbol). By not encoding in UTF-8, special characters may not be saved correctly and Minecraft may fail to parse the data.

**File/folder names must all be lowercase to circumvent issues with differing operating system file structures.**

### Windows Notepad

Windows Notepad is capable of saving as UTF-8, though will default to ANSI. Instructions for saving a `.json` in UTF-8:

1. Click *Save* or *Save As*.
2. Set *"Save as type"* to *"All files (*.*)"*.
3. Name your file and append the name with `.json`.
4. Set *"Encoding"* to *"UTF-8"*.
5. Save.

Image example:

![](http://skylinerw.com/images/advancements/editing_1.png)

### Notepad++

[Notepad++](https://notepad-plus-plus.org) is a great alternative to using plain Notepad. Instructions for saving a `.json` in UTF-8:

1. Change the encoding to either *"UTF-8"* or *"UTF-8 without BOM"* under the *"Encoding"* menu. See image below.
2. Click *Save* or *Save As*.
3. Set *"Save as type"* to *"All files (*.*)"*.
4. Name your file and append the name with `.json`.
5. Save.

Image example:

![](http://skylinerw.com/images/advancements/editing_2.png)

### Atom

[Atom](https://atom.io) has multi-platform support. Instructions for saving a `.json` in UTF-8:

1. Change the encoding to *"UTF-8"* under the *"Select Encoding"* menu. See image below.
2. Click *Save* or *Save As*.
3. Set *"Save as type"* to *"All files (*.*)"*.
4. Name your file and append the name with `.json`.
5. Save.

Image example:

![](http://skylinerw.com/images/advancements/editing_3.png)

# Shared data structures

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="structure">JSON structure</a>

The following is a list of all possible keys for advancements.

```json
{
    "criteria": {
        "custom_trigger_name": {
            "trigger": "namespace:trigger_name",
            "conditions": {
                "durability": 1,
                "durability": {"min":1,"max":1},
                "delta": 1,
                "delta": {"min":1,"max":1},
                "slots": {
                    "occupied": 1,
                    "occupied": {"min":1,"max":1},
                    "full": 1,
                    "full": {"min":1,"max":1},
                    "empty": 1,
                    "empty": {"min":1,"max":1}
                },
                "items": [
                    {
                        "item": "minecraft:stone",
                        "tag": "minecraft:doors",
                        "count": 1,
                        "count": {"min":1,"max":1},
                        "durability": 1,
                        "durability": {"min":1,"max":1},
                        "potion": "minecraft:invisibility",
                        "enchantments": [
                            {
                                "enchantment": "minecraft:sharpness",
                                "levels": 1,
                                "levels": {"min":1,"max":1}
                            }
                        ]
                    }
                ],
                "item": {
                    "item": "minecraft:stone",
                    "tag": "minecraft:doors",
                    "count": 1,
                    "count": {"min":1,"max":1},
                    "durability": 1,
                    "durability": {"min":1,"max":1},
                    "potion": "minecraft:invisibility",
                    "enchantments": [
                        {
                            "enchantment": "minecraft:sharpness",
                            "levels": 1,
                            "levels": {"min":1,"max":1}
                        }
                    ]
                },
                "levels": 1,
                "levels": {"min":1,"max":1},
                "recipe": "minecraft:chest",
                "position": {
                    "x": 1,
                    "x": {"min":1,"max":1},
                    "y": 1,
                    "y": {"min":1,"max":1},
                    "z": 1,
                    "z": {"min":1,"max":1}
                },
                "biome": "minecraft:void",
                "feature": "EndCity",
                "dimension": "overworld",
                "from": "overworld",
                "to": "overworld",
                "block": "minecraft:stone",
                "state": {
                    "state_name": "state_value"
                },
                "entity": {
                    "type": "minecraft:creeper",
                    "distance": 1,
                    "distance": {"min":1,"max":1}
                },
                "killing_blow": {
                    "bypasses_armor": true,
                    "bypasses_invulnerability": true,
                    "bypasses_magic": true,
                    "is_explosion": true,
                    "is_fire": true,
                    "is_magic": true,
                    "is_projectile": true,
                    "direct_entity": {
                        "type": "minecraft:creeper",
                        "distance": 1,
                        "distance": {"min":1,"max":1}
                    },
                    "source_entity": {
                        "type": "minecraft:creeper",
                        "distance": 1,
                        "distance": {"min":1,"max":1}
                    }
                },
                "distance": 1,
                "distance": {"min":1,"max":1},
                "parent": {
                    "type": "minecraft:creeper",
                    "distance": 1,
                    "distance": {"min":1,"max":1}
                },
                "partner": {
                    "type": "minecraft:creeper",
                    "distance": 1,
                    "distance": {"min":1,"max":1}
                },
                "child": {
                    "type": "minecraft:creeper",
                    "distance": 1,
                    "distance": {"min":1,"max":1}
                },
                "potion": "minecraft:invisibility",
                "level": 1,
                "level": {"min":1,"max":1},
                "damage": {
                    "dealt": 1,
                    "dealt": {"min":1,"max":1},
                    "taken": 1,
                    "taken": {"min":1,"max":1},
                    "blocked": true,
                    "type": {
                        "bypasses_armor": true,
                        "bypasses_invulnerability": true,
                        "bypasses_magic": true,
                        "is_explosion": true,
                        "is_fire": true,
                        "is_magic": true,
                        "is_projectile": true,
                        "direct_entity": {
                            "type": "minecraft:creeper",
                            "distance": 1,
                            "distance": {"min":1,"max":1}
                        },
                        "source_entity": {
                            "type": "minecraft:creeper",
                            "distance": 1,
                            "distance": {"min":1,"max":1}
                        }
                    },
                    "source_entity": {
                        "type": "minecraft:creeper",
                        "distance": 1,
                        "distance": {"min":1,"max":1}
                    }
                }
            }
        }
    },
    "requirements": [["generic_trigger_name"]],
    "parent": "namespace:path/to/parent_advancement",
    "display": {
        "icon": {
            "item": "minecraft:stone_pickaxe"
        },
        "title": "Display title",
        "description": "Display description",
        "frame": "task",
        "background": "minecraft:path/to/texture.png"
    },
    "rewards": {
        "recipes": ["namespace:path/to/recipe"],
        "loot": ["namespace:path/to/loot_table"],
        "experience": 1,
        "function": "path:to/function"
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-range">Shared: range</a>

A large number of advancement features make use of a "range" object, which is a comparison of the incoming number (such as the number of occupied inventory slots) to the specified range of numbers.

To check for an exact value, simply declare the range as a number. The following checks if the compared value is exactly 3.

```json
"occupied": 3
```

To check between two values, the range must be specified as an object containing `min` and `max` numbers. The following checks if the compared value is between 1 and 3.

```json
"occupied": {
    "min": 1,
    "max": 3
}
```

You can alternatively specify only the minmum or only the maximum, which will ignore a check for the opposing limiter. For example, the following checks if there are *at least* 3 occupied slots. The player's inventory could have 7 occupied slots and they will still match.

```json
"occupied": {
    "min": 3
}
```

Versus the opposite, where the following checks if there are *at most* 2 occupied slots. The player's inventory could have 0 occupied slots and they will still match.

```json
"occupied": {
    "max": 2
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-location">Shared: location object</a>

A location object contains a small amount of data to specify an origin, biome, or generated structure. All options are available anywhere that this location object is used.

### 1. `position`

The `position` object contains `x`, `y`, and `z` [ranges](#generic-range) to check the global position of the player in the world. You do not need to specify all of the axes. For example, the following checks if the player is at Y62 or lower.

```json
"location_object": {
    "position": {
        "y": {
            "max": 62.0
        }
    }
}
```

### 2. `biome`

The `biome` string specifies the name ID of the biome that the player must stand within. You can find a list of name IDs for biomes [here](https://pastebin.com/MK61Xuzf). The following checks if the player has visited the "minecraft:desert" biome.

```json
"location_object": {
    "biome": "minecraft:desert"
}
```

### 3. `feature`

The `feature` string specifies the name ID of a structure. The player must be standing within the bounding box of that structure to be detected. The following checks if the player is inside an "EndCity" structure.

```json
"location_object": {
    "feature": "EndCity"
}
```

### 4. `dimension`

The `dimension` string specifies the name ID of a dimension to find the player in. Accepted values are "overworld", "the_nether", and "the_end". The following checks if the player is anywhere in the nether.

```json
"location_object": {
    "dimension": "the_nether"
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-distance">Shared: distance object</a>

The distance object contains information about the distance between the advancement-earning player and an unspecified origin. The origin cannot be directly defined but changes depending on the trigger; see each [trigger](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md) that uses this for specific information. All options are available anywhere that this location object is used.

### 1. `x/y/z`

The `x`, `y`, and `z` [ranges](#generic-range) each check if the player is a number of blocks in either direction of the origin in the specified axis. You do not need to specify all of the axes. For example, the following checks if the player is within 40 blocks in either the positive or negative X direction of the origin.

```json
"distance_object": {
    "x": {
        "max": 40.0
    }
}
```

### 2. `absolute`

The `absolute` [range](#generic-range) checks if the player is within a number of blocks on all axes. You would use this instead of `x/y/z` if all axes are uniform. The following checks if the player is outside a 5-block range of an origin.

```json
"distance_object": {
    "absolute": {
        "min": 5.0
    }
}
```

### 3. `horizontal`

The `horizontal` [range](#generic-range) checks if the player is within a number of blocks on the X and Z axes, ignoring the Y axis. The following checks if the player is withn a 10-block range of an origin, but only on the X and Z axes.

```json
"distance_object": {
    "horizontal": {
        "max": 10.0
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-effects">Shared: status effects object</a>

A status effect object contains nested objects, whose key names reflect [status effect IDS](https://minecraft.gamepedia.com/Data_values#Status_effects). All options are available anywhere that this item object is used.

### 1. `amplifier`

The `amplifier` [range](#generic-range) checks the amplifier of the specified effect. The following checks if the amplifier is 10 or higher.

```json
"effects_object": {
    "minecraft:levitation": {
        "amplifier": {
            "min": 10
        }
    }
}
```

### 2. `duration`

The `duration` [range](#generic-range) checks the remaining duration in ticks of the specified effect. The following checks if the effect has at least 15 seconds (300 ticks) remaining.

```json
"effects_object": {
    "minecraft:levitation": {
        "duration": {
            "max": 300
        }
    }
}
```

### 3. `ambient`

The `ambient` boolean checks if the effect has the "ambient" flag set to true.

```json
"effects_object": {
    "minecraft:levitation": {
        "ambient": true
    }
}
```

### 4. `visible`

The `visible` boolean checks if the effect has the "visible" flag set to true.

```json
"effects_object": {
    "minecraft:levitation": {
        "visible": true
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-item">Shared: item object</a>

An item object contains a handful of data to compare to an incoming item stack. All options are available anywhere that this item object is used.

### 1. `item`, `tag`

**You cannot use both `item` and `tag` together.**

The `item` string specifies a base item ID to compare the item to. The following checks if the item is redstone.

```json
"item_object": {
    "item": "minecraft:redstone"
}
```

The `tag` string instead specifies the resource location to an ID group, minus the designating `#` character. These groups are a list of item IDs that the incoming item can match any one of. Default groups for items include: "#minecraft:wool", "#minecraft:planks", "#minecraft:stone_bricks", "#minecraft:wooden_buttons", "#minecraft:buttons", "#minecraft:carpets", "#minecraft:wooden_doors", "#minecraft:doors", "#minecraft:saplings", and "#minecraft:logs". Adding custom groups requires the use of [data packs](https://minecraft.gamepedia.com/Data_pack). The following checks if the item matches any of the IDs in the "minecraft:doors" group (which checks both the "#minecraft:wooden_doors" group as well as the "minecraft:iron_door" item ID).

```json
"item_object": {
    "tag": "minecraft:doors"
}
```

### 2. `durability`

The `durability` [range](#generic-range) specifies the remaining durability of an item. The following checks if the item has 400 or more durability remaining.

```json
"item_object": {
    "durability": {
        "min": 400
    }
}
```

### 3. `count`

The `count` [range](#generic-range) specifies the number of items **in a single stack**. This cannot be used to check the number of items across the inventory as a whole. The following checks if the item has 16 or more in its stack.

```json
"item_object": {
    "count": {
        "min": 16
    }
}
```

### 4. `potion`

The `potion` string specifies the default brewed potion ID that the item must contain, specified in the `Potion` NBT string. The wiki contains a list of those IDs [here](https://minecraft.gamepedia.com/Potion#Item_data).

```json
"item_object": {
    "potion": "minecraft:invisibility"
}
```

**The item does not have to be a potion**. As long as the item has the `Potion` NBT string, it will match:

```
/give @p minecraft:stone 1 0 {Potion:"minecraft:invisibility"}
```

### 5. `enchantments`

The `enchantments` list checks the item's enchantments (whether in the `ench` NBT list for all items excluding books, or the `StoredEnchantments` NBT list for only books) for matching data. If only an empty object is specified, the player's inventory is checked for **any** enchanted items.

```json
"item_object": {
    "enchantments": [
        {
            
        }
    ]
}
```

The `enchantment` string will specify the enchantment ID to look for. The following checks the item for the Sharpness enchantment.

```json
"item_object": {
    "enchantments": [
        {
            "enchantment": "minecraft:sharpness"
        }
    ]
}
```

The `levels` [range](#generic-range) will specify the range of levels to find for an enchantment. The following checks if the player has any enchantments level 3 or higher.

```json
"item_object": {
    "enchantments": [
        {
            "levels": {
                "min": 3
            }
        }
    ]
}
```

And combining it with an ID, the following checks if the player has Sharpness 5.

```json
"item_object": {
    "enchantments": [
        {
            "enchantment": "minecraft:sharpness",
            "levels": 5
        }
    ]
}
```

### 6. `nbt`

The `nbt` string compares the raw NBT input to the item's NBT data. This raw data starts within the `tag` compound of the item format and must be surrounded by curly brackets. The following checks if the item has a specific display name.

```json
"item_object": {
    "nbt": "{display:{Name:\"Test\"}}"
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-entity">Shared: entity object</a>

An entity object contains a handful of data to compare to an incoming entity. All options are available anywhere that this entity object is used.

### 1. `type`

The `type` string specifies the entity ID to match against. For example, the following checks if the incoming entity is a creeper.

```json
"entity_object": {
    "type": "minecraft:creeper"
}
```

### 2. `distance`

The `distance` [distance object](#generic-distance) specifies the distance between the advancement-earning player and an entity's origin. The following checks if the player is within 10 blocks of the entity.

```json
"entity_object": {
    "distance": {
        "absolute": {
            "max": 10
        }
    }
}
```

### 3. `location`

The `location` [location object](#generic-location) checks data concerning the entity's location in the world. The following checks if the entity is in the desert biome.

```json
"entity_object": {
    "location": {
        "biome": "minecraft:desert"
    }
}
```

### 4. `effects`

The `effects` [status effects object](#generic-effects) checks data concerning the entity's status effects. The following checks if the entity has Speed 2.

```json
"entity_object": {
    "effects": {
        "minecraft:speed": {
            "amplifier": 2
        }
    }
}
```

### 5. `nbt`

The `nbt` string compares the raw NBT input to the entity's NBT data. This raw data starts within the root of the entity format and must be surrounded by curly brackets. If the entity being checked is a player, the `SelectedItem` compound (containing the player's mainhand item) will be appended to the player's NBT before comparing to the provided input. The following checks if the entity has a specific tag within its `Tags` list.

```json
"entity_object": {
    "nbt": "{Tags:[\"findme\"]}"
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-block">Shared: block object</a>

A block object contains a handful of data to compare to an incoming block. All options are available anywhere that this entity object is used.

### 1. `block`

The `block` string specifies a base block ID to detect the player in. For example, the following checks if the block has the base ID of "minecraft:tallgrass" (includes grass, fern, and double tallgrass).

```json
"block_object": {
    "block": "minecraft:tallgrass"
}
```

### 2. `state`

The `state` object contains a list of custom keys, much like `criteria` does. The names for these keys will correspond to the blockstate name you want to detect, and the value corresponds to possible values for that blockstate. For "minecraft:tallgrass", the `type` blockstate specifies which of the tallgrass blocks it is. The `block` string **must** be specified to use this condition. The following checks if the tallgrass is a fern.

```json
"block_object": {
    "block": "minecraft:tallgrass",
    "state": {
        "type": "fern"
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-damage">Shared: damage object</a>

A damage object contains a large amount of information about the incoming or outgoing damage. All options are available anywhere that this damage object is used.

### 1. `dealt`

A [range](#generic-range) checking the raw incoming damage **before** damage reduction. For example, the following checks the damage of an un-owned arrow (via /summon) before that damage was either reduced or blocked with a shield entirely.

```json
"damage_object": {
    "dealt": {
        "min": 4.0
    },
    "source_entity": {
        "type": "minecraft:arrow"
    }
}
```

### 2. `taken`

A [range](#generic-range) checking the incoming damage **after** damage reduction. For example, the following checks if the resulting damage after reductions was at least 5.0.

```json
"damage_object": {
    "taken": {
        "min": 5.0
    }
}
```

### 3. `blocked`

Checks if the incoming damage was successfully blocked, provided that the `damage_flags_object.bypasses_armor` ("unblockable") flag isn't true. The following checks if the player **failed** to block a skeleton's attack (either by an arrow or melee weapon).

```json
"damage_object": {
    "blocked": false,
    "source_entity": {
        "type": "minecraft:skeleton"
    }
}
```

### 4. `type`

A [damage flags object](#generic-damage-flags) that checks various flags about the damage. The following checks if the damage was a projectile caused by a skeleton (though does not necessarily mean the direct cause of the damage was an arrow).

```json
"damage_object": {
    "type": {
        "is_projectile": true
    },
    "source_entity": {
        "type": "minecraft:skeleton"
    }
}
```

### 5. `source_entity`

An [enity object](#generic-entity) that checks information about either the entity hit or the entity dealing the damage. Note that for players being the source entity, the nested `type` string can essentially only be "minecraft:player". The following checks if a skeleton was at least 10 blocks away when it hit the player.

```json
"damage_object": {
    "source_entity": {
        "type": "minecraft:skeleton",
        "distance": {
            "min": 10
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-damage-flags">Shared: damage flags object</a>

A damage flags object checks different flags concerning the incoming or outgoing damage. All options are available anywhere that this damage flags object is used.

### 1. `bypasses_armor`

Checks the "unblockable" flag for the incoming damage. This is true for: fire, suffocation (blocks & world border), entity cramming, drowning, starving, falling, flying into a wall, void (the Void & `/kill`), health recalcuation, magic damage, and wither effect damage. The following checks if the damage is caused by any damage that cannot be blocked.

```json
"damage_object": {
    "bypasses_armor": true
}
```

### 2. `bypasses_invulnerability`

Checks if the damage source can inflict damage on creative mode players. This is true for: void damage. The following checks if the incoming damage is **not** caused by the Void or `/kill`.

```json
"damage_object": {
    "bypasses_invulnerability": false
}
```

### 3. `bypasses_magic`

Checks the "damageIsAbsolute" flag for the incoming damage. This is true for: starvation. The following checks if the player is taking starvation damage.

```json
"damage_object": {
    "bypasses_magic": true
}
```

### 4. `is_explosion`

Checks the "explosion" flag for the incoming damage. This is true for: creepers, ender crystals, TNT, Minecart TNT, ghast fireballs, beds, the wither, and wither skulls. The following checks if the "explosion" flag is true.

```json
"damage_object": {
    "explosion": true
}
```

### 5. `is_fire`

Checks the "fire" flag for the incoming damage. This is true for: standing in a fire block, being on fire, standing on magma, standing in lava, ghast fireballs, and blaze fireballs. The following checks if the player is **not** taking damage from fire sources.

```json
"damage_object": {
    "is_fire": false
}
```

### 6. `is_magic`

Checks the "magicDamage" flag for the incoming damage. This is true for: thorns, Instant Damage effect, Poison effect, part of Guardian laser damage, evocation fangs, and un-owned wither skulls (via `/summon`). The following checks if the incoming damage is flagged as magic damage.

```json
"damage": {
    "is_magic": true
}
```

### 7. `is_projectile`

Checks the "projectile" flag for the incoming damage. This is true for: arrows, ghast fireballs, blaze fireballs, enderpearls, eggs, snowballs, shulker bullets, and llama spit. The following checks if the incoming damage is specifically **not** a projectile.

```json
"damage": {
    "is_projectile": false
}
```

### 8. `source_entity`

An [enity object](#generic-entity) that specifies the "owner" of the damage. For example, if the player was hit by an arrow shot by a skeleton, the skeleton would be the "source entity". The [damage object](#generic-damage) already makes use of this check, so it is pointless to specify a source entity twice. This particular check is still useful for triggers that only use a damage flags object rather than a damage object.

```json
"damage": {
    "source_entity": {
        "type": "minecraft:skeleton"
    }
}
```

### 9. `direct_entity`

An [enity object](#generic-entity) that specifies the direct cause of the damage. For example, if the player was hit by an arrow shot by a skeleton, the arrow would be the "direct entity". The following ensures the player was hit by an arrow shot by a skeleton.

```json
"damage": {
    "direct_entity": {
        "type": "minecraft:arrow"
    },
    "source_entity": {
        "type": "minecraft:skeleton"
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-death">Shared: death object</a>

The death object contains information about both the killer/killed entity (changing depending on the trigger) and the killing blow. All options are available anywhere that this death object is used.

### 1. `entity`

An [enity object](#generic-entity) that will either describe the entity that was killed or the killer itself, depending on the trigger being used; see each [trigger](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md) that uses this for specific information. To be clear: this does not always describe the entity that died. The following checks if the killed entity or killer was a creeper.

```json
"death_object": {
    "entity": {
        "type": "minecraft:creeper"
    }
}
```

### 2. `killing_blow`

A [damage flags object](#generic-damage-flags) that contains damage flags about the killing blow against the dead entity. Be aware that this is **not** the [damage object](#generic-damage), so the options from that object are unavailable. The following checks if the entity had died due to an explosion.

```json
"death_object": {
    "killing_blow": {
        "is_explosion": true
    }
}
```

# Customizing advancements

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="criteria">Criteria</a>

*[List of triggers](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md)*

An advancement **must** include a set of rules that activate the advancement, specified in the `criteria` object. Each object nested within will contain a custom name of your choosing, to later be used with the optional [`requirements`](#requirements) list or the [`/advancement`](#command-advancement) command. When the player matches a criterion at any point, that fulfillment will be remembered, allowing the player to fulfill all criteria over time rather than all at the same time.

Within each criterion there **must** be a [trigger](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md), specified by the `trigger` string.

If `requirements` is not specified, then all criteria must be met for the advancement to be granted.

The following advancement is granted provided **both** the "custom_test_name" and "take_damage" criteria succeeds, where the names can be anything you want. The player can breed animals together, and then at any point in the future take damage to complete the advancement (or vice versa). As `requirements` is not specified, both criteria must be fulfilled.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals"
        },
        "take_damage": {
            "trigger": "minecraft:entity_hurt_player"
        }
    }
}
```

While the following advancement is granted provided **either** the "custom_test_name" and "take_damage" criteria succeeds. See the [Requirements](#requirements) section for more details.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals"
        },
        "take_damage": {
            "trigger": "minecraft:entity_hurt_player"
        }
    },
    "requirements": [["custom_test_name"], ["take_damage"]]
}
```

The custom criteria names are local to that file and cannot be referred to in other files.

### <a name="criteria-order">Processing order of triggers</a>

The following timeline describes the order in which specific triggers activate in each game tick. This can be useful when running a function as a [reward](#rewards). For example, if you needed to run commands before entities are processed (and after command blocks are processed), the "minecraft:tick" trigger will do so. If you needed to run commands after entities are processed, you would use the "gameLoopFunction" gamerule to run the necessary function.

![](http://i.imgur.com/meB4V6V.png)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="requirements">Requirements</a>

The `requirements` list specifies a [Conjunctive Normal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form) structure to accompany criteria. It essentially allows boolean logic to determine when the advancement is granted based on the accompanying criteria. The list contains more lists, which in turn contains strings that equal the names of the criteria. You would use this if you want to use an OR operation. Each new list is a new AND operation, while each comma-separated string within the list is an OR operation.

If `requirements` is not specified, then **all** criteria must be fulfilled. If this list *is* specified, then every criteria must be included in `requirements` to determine which ones need to be fulfilled.

Given the following advancement:

```json
{
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:entity_hurt_player"
        },
        "trigger_2": {
            "trigger": "minecraft:used_ender_eye"
        }
    },
    "requirements": [
        ["trigger_1"], ["trigger_2"]
    ]
}
```

Both "trigger_1" and "trigger_2" criteria must be fulfilled before the advancement can be granted. Since that would be the case without `requirements`, it is not necessary to use it here. Using logical operators, this can be viewed as:

```
"trigger_1" && "trigger_2"
```

Modifying the requirements list slightly, which joins "trigger_2" with "trigger_1":

```json
{
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:entity_hurt_player"
        },
        "trigger_2": {
            "trigger": "minecraft:used_ender_eye"
        }
    },
    "requirements": [
        ["trigger_1", "trigger_2"]
    ]
}
```

Now **either** "trigger_1" or "trigger_2" must be completed to be granted the advancement. Using logical operators, this can be viewed as:

```
"trigger_1" || "trigger_2"
```

Given the following advancement:

```json
{
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:entity_hurt_player"
        },
        "trigger_2": {
            "trigger": "minecraft:used_ender_eye"
        },
        "trigger_3": {
            "trigger": "minecraft:player_killed_entity"
        }
    },
    "requirements": [
        ["trigger_1"],
        ["trigger_2", "trigger_3"]
    ]
}
```

The advancement is granted if the "trigger_1" criterion is met, while either "trigger_2" and "trigger_3" are also met. If only "trigger_1" is completed, the advancement is not granted. Using logical operators, this can be viewed as:

```
"trigger_1" && ("trigger_2" || "trigger_3")
```

And modifying the requirements slightly:

```json
{
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:entity_hurt_player"
        },
        "trigger_2": {
            "trigger": "minecraft:used_ender_eye"
        },
        "trigger_3": {
            "trigger": "minecraft:player_killed_entity"
        }
    },
    "requirements": [
        ["trigger_1", "trigger_2", "trigger_3"]
    ]
}
```

The advancement is granted if any of the criteria are met. Using logical operators, this can be viewed as:

```
"trigger_1" || "trigger_2" || "trigger_3"
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display">Display</a>

The optional `display` object contains information about the display in the "Advancements" menu. If this object does not exist, the advancement will be hidden from the "Advancements" menu and popup notifications will not appear.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-title-desc-icon">Title, description, & icon</a>

When the `display` object exists, the `title`, `description`, and `icon` tags **must** be specified. The background for the icon for each advancement will range from gray to red depending on the number of criteria fulfilled in that advancement.

#### 1. `title`

The title can either be a simple string or a [text component](https://github.com/skylinerw/guides/blob/master/java/text%20component.md) object. This title is shown in the "Advancements" menu when hovering over the icon, as well as in the popup notification when completing the advancement.

Given the following advancement, which uses a simple string for the title:

```json
{
    "display": {
        "title": "Stew",
        "description": "",
        "icon": {
            "item": "minecraft:mushroom_stew"
        },
        "background": "minecraft:textures/gui/advancements/backgrounds/stone.png"
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:location"
        }
    }
}
```

This will display as:

![](http://skylinerw.com/images/advancements/display_1.png)

Or you can specify formatting for the text using the text component (note that many text component features are unavailable, such as selectors, scores, and event listeners):

```json
{
    "display": {
        "title": {"text":"Stew","obfuscated":true},
        "description": "",
        "icon": {
            "item": "minecraft:mushroom_stew"
        },
        "background": "minecraft:textures/gui/advancements/backgrounds/stone.png"
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:location"
        }
    }
}
```

This will display as:

![](http://skylinerw.com/images/advancements/display_4.png)

#### 2. `description`

The description can either be a simple string or a [text component](https://github.com/skylinerw/guides/blob/master/java/text%20component.md) object. This description is shown in the "Advancements" menu when hovering over the icon, but **not** in the popup notification when completing the advancement. The description can be blank for no description to appear, but the tag itself must still exist.

Given the following advancement, which uses a simple string for the description:

```json
{
    "display": {
        "icon": {
            "item": "minecraft:elytra"
        },
        "title": "Learn to Fly",
        "description": "Learn how to fly using a new pair of elytra.",
        "background": "minecraft:textures/gui/advancements/backgrounds/stone.png"
    },
    "criteria": {
        "elytra": {
            "trigger": "minecraft:inventory_changed",
            "conditions": {
                "items": [
                    {
                        "item": "minecraft:elytra",
                        "durability": 431
                    }
                ]
            }
        }
    }
}
```

This will display as:

![](http://skylinerw.com/images/advancements/display_6.png)

Or you can specify formatting for the text using the text component (note that many text component features are unavailable, such as selectors, scores, and event listeners):

```json
{
    "display": {
        "icon": {
            "item": "minecraft:elytra"
        },
        "title": "Learn to Fly",
        "description": {"text":"Learn how to fly using a new pair of elytra.","italic":true},
        "background": "minecraft:textures/gui/advancements/backgrounds/stone.png"
    },
    "criteria": {
        "elytra": {
            "trigger": "minecraft:inventory_changed",
            "conditions": {
                "items": [
                    {
                        "item": "minecraft:elytra",
                        "durability": 431
                    }
                ]
            }
        }
    }
}
```

This will display as:

![](http://skylinerw.com/images/advancements/display_7.png)

#### 3. `icon`

The `icon` object holds a required item ID of the item. This icon is shown in the "Advancements" menu, as well as in the popup notification when completing the advancement. The following icon specifies red wool:

```json
"icon": {
    "item": "minecraft:red_wool"
}
```

Currently you may not specify any NBT data for the item, including durability.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-background">Background</a>

The `background` is an optional string for all advancements, but is only used by "root" advancements (i.e. when no [parent](#tree-branch) is defined). This is the background that appears behind all of the icons. The value is a resource location to any image within a resource pack. For example, the following advancement will display a tiled gold block background, coming from the default block texture:

```json
{
    "display": {
        "title": "Stew",
        "description": "",
        "icon": {
            "item": "minecraft:mushroom_stew"
        },
        "background": "minecraft:textures/blocks/gold_block.png"
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:location"
        }
    }
}
```

Image of the result:

![](http://skylinerw.com/images/advancements/display_2.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-frame">Frame</a>

An optional `frame` string modifies the border shape around the icon, accepting one of three possible values: "task", "challenge", and "goal". When not specified, it will default to "task".

The following advancement makes use of the "challenge" frame.

```json
{
    "display": {
        "title": "Stew",
        "description": "",
        "icon": {
            "item": "minecraft:mushroom_stew"
        },
        "background": "minecraft:textures/blocks/gold_block.png",
        "frame": "challenge"
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:location"
        }
    }
}
```

![](http://skylinerw.com/images/advancements/display_3.png)

The following advancement makes use of the "goal" frame.

```json
{
    "display": {
        "title": "Stew",
        "description": "",
        "icon": {
            "item": "minecraft:mushroom_stew"
        },
        "background": "minecraft:textures/blocks/gold_block.png",
        "frame": "goal"
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:location"
        }
    }
}
```

![](http://skylinerw.com/images/advancements/display_5.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-toast">Show toast</a>

An optional `show_toast` boolean can be specified in order to prevent the popup notification in the upper right-hand corner of the screen from appearing when the player fulfills an advancement. It defaults to true, and setting it to false will hide the popup.

```json
{
    "display": {
        "title": "Stew",
        "description": "",
        "icon": {
            "item": "minecraft:stone"
        },
        "background": "minecraft:textures/blocks/gold_block.png",
        "show_toast": false
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:tick"
        }
    }
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-announce">Announce to chat</a>

An optional `announce_to_chat` boolean can be specified in order to prevent a chat message from being sent telling all players that somebody fulfilled an advancement. It defaults to true, and setting it to false will prevent chat messages from being sent.

```json
{
    "display": {
        "title": "Stew",
        "description": "",
        "icon": {
            "item": "minecraft:stone"
        },
        "background": "minecraft:textures/blocks/gold_block.png",
        "announce_to_chat": false
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:tick"
        }
    }
}
```

The `announceAdvancements` gamerule can globally disable fulfillment messages, overriding the value of `announce_to_chat` for individual advancements.

```
/gamerule announceAdvancements false
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-hidden">Hidden</a>

An optional `hidden` boolean can be specified in order to prevent child advancements from displaying in the "Advancements" menu until they are completed.

```json
{
    "display": {
        "title": "Stew",
        "description": "",
        "icon": {
            "item": "minecraft:stone"
        },
        "background": "minecraft:textures/blocks/gold_block.png",
        "announce_to_chat": false,
        "hidden": true
    },
    "criteria": {
        "trigger_1": {
            "trigger": "minecraft:impossible"
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards">Rewards</a>

The `rewards` object specifies multiple types of rewards to provide the player upon completing the advancement.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-recipes">`recipes`</a>

This list specifies multiple recipes to unlock for the player upon completing the advancement. The following advancement unlocks the "minecraft:redstone" and "minecraft:ladder" recipes together.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player"
        }
    },
    "rewards": {
        "recipes": ["minecraft:redstone", "minecraft:ladder"]
    }
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-loot">`loot`</a>

This list specifies multiple loot tables to process, providing the player with the resulting item(s). The following advancement provides players with items from "minecraft:entities/creeper" and "minecraft:chests/simple_dungeon".

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player"
        }
    },
    "rewards": {
        "loot": ["minecraft:entities/creeper", "minecraft:chests/simple_dungeon"]
    }
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-experience">`experience`</a>

This number (**not** range) specifies the amount of experience (**not** levels) to reward the player with.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player"
        }
    },
    "rewards": {
        "experience": 500
    }
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-function">`function`</a>

This string specifies a single function file to run, with the value being the resource location to that function. The player will be considered the command sender and CommandStats trigger target, which means sender bias (such as from "@s") will always target that player, and that player will trigger their own stored CommandStats. Each command will run in the specified order in the function. If the advancement is granted via command block, all of the listed commands will execute immediately, allowing other command blocks further in the chain to run based off of the results of the advancement's function being run (although you do not need to use advancements for this if focusing on functions).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player"
        }
    },
    "rewards": {
        "function": "path:to/function"
    }
}
```

***

All rewards can be used at the same time.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player"
        }
    },
    "rewards": {
        "recipes": ["minecraft:redstone", "minecraft:ladder"],
        "loot": ["minecraft:entities/creeper", "minecraft:chests/simple_dungeon"],
        "experience": 500,
        "function": "path:to/function"
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tree">Tree display</a>

When an advancement has a [display](#display), it will be shown in the "Advancements" menu under a relevant tab. [Root](#tree-root) advancements will be the "owner" of a tab, with new tabs appearing for each unique root:

![](http://skylinerw.com/images/advancements/tree_1.png)

Any advancements referring to the root will be its [children](#tree-branch) and are displayed in a branching tree:

![](http://skylinerw.com/images/advancements/tree_2.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tree-root">Root</a>

A root advancement will be one without a [parent](#tree-branch) defined. If a root has a display, it will be the left-most icon in the tree. If a root does not have a display, a tab in the "Advancements" menu will not be shown, effectively hiding all branches in the tree. The popup notification for branches **will** be displayed even in that case, allowing you to show the player custom notifications without having to have a tab in the "Advancements" menu.

A root can also control the [background](#display-background) of the tab.

The following is a proper root that will be shown in the menu, as it has both a display and no parent:

```json
{
    "display": {
        "icon": {
            "item": "minecraft:diamond"
        },
        "title": "A Root",
        "description": "The root of this tree.",
        "background": "minecraft:textures/gui/advancements/backgrounds/stone.png"
    },
    "criteria": {
        "impossible": {
            "trigger": "minecraft:impossible"
        }
    }
}
```

While the following is an invisible root, as it has neither a display nor a parent:

```json
{
    "criteria": {
        "impossible": {
            "trigger": "minecraft:impossible"
        }
    }
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tree-branches">Branches</a>

A branch is defined by having a `parent` string. The parent specifies the resource location of another advancement, where the branch visually extends from the parent. Note that the parent does **not** actually need to be completed in order for the branch to be completed.

The visual structure of the tree will be automatically generated.

For example, the following set of advancements defines a root and two branches, where the branches are direct descendents of the root:

`/custom/root.json`
```json
{
    "display": {
        "icon": {
            "item": "minecraft:sapling"
        },
        "title": "A Root",
        "description": "The root of this tree.",
        "background": "minecraft:textures/gui/advancements/backgrounds/stone.png"
    },
    "criteria": {
        "auto": {
            "trigger": "minecraft:location"
        }
    }
}
```

`/custom/branch_a.json`
```json
{
    "display": {
        "icon": {
            "item": "minecraft:stick"
        },
        "title": "Branch 1",
        "description": "One branch of the tree."
    },
    "parent": "custom:root",
    "criteria": {
        "auto": {
            "trigger": "minecraft:location"
        }
    }
}
```

`/custom/branch_b.json`
```json
{
    "display": {
        "icon": {
            "item": "minecraft:blaze_rod"
        },
        "title": "Branch 2",
        "description": "Another branch of the tree."
    },
    "parent": "custom:root",
    "criteria": {
        "auto": {
            "trigger": "minecraft:location"
        }
    }
}
```

Visual:

![](http://skylinerw.com/images/advancements/tree_3.png)

Changing `/custom/branch_b.json` to the following will cause it to branch off of "custom:branch_a" rather than the root:

```json
{
    "display": {
        "icon": {
            "item": "minecraft:blaze_rod"
        },
        "title": "Branch 2",
        "description": "Another branch of the tree."
    },
    "parent": "custom:branch_a",
    "criteria": {
        "auto": {
            "trigger": "minecraft:location"
        }
    }
}
```

Which would then show as a direct line of branches:

![](http://skylinerw.com/images/advancements/tree_4.png)

Since you can have multiple branches per parent, complex trees can be created. The following image is a segment from the default "story" tab for vanilla Minecraft, showing complex connections between branches:

![](http://skylinerw.com/images/advancements/tree_5.png)

# Commands

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="command-advancement">Command: `/advancement`</a>

*Coming soon...*

# Conclusion

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="qa">Q&A</a>

*Coming soon...*

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="external-links">External links</a>

- [Generic JSON Validator](http://jsonlint.com)
- [Minecraft Wiki: Advancements](http://minecraft.gamepedia.com/Advancements)

Translations:

- [Chinese](https://pca006132.neocities.org/tutorials/advancement/advancement.html)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conclusion">Conclusion</a>

If there is information that needs to be added, corrected, or clarified, please create a new issue!
