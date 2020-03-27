# Advancements

*Last updated: 1.14.3-pre4*

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

### Data structures

7. [Complete JSON structure](#structure)
8. [Shared data structures](https://github.com/skylinerw/guides/blob/master/java/advancements/data_structures.md)

### Customizing advancements

9. [Criteria](#criteria)
    1. [List of triggers](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md)
    2. [Processing order of triggers](#criteria-order)
10. [Requirements](#requirements)
11. [Display](#display)
    1. [Title, description, & icon](#display-title-desc-icon)
    2. [Background](#display-background)
    3. [Frame](#display-frame)
    4. [Show toast](#display-toast)
    5. [Announce to chat](#display-announce)
    6. [Hidden](#display-hidden)
12. [Rewards](#rewards)
    1. [`recipes`](#rewards-recipes)
    2. [`loot`](#rewards-loot)
    3. [`experience`](#rewards-experience)
    4. [`function`](#rewards-function)
13. [Tree display](#tree)
    1. [Root](#tree-root)
    2. [Branches](#tree-branches)

### Commands

14. [Command: `/advancement`](#command-advancement)

### Conclusion

15. [Q&A](#qa)
16. [External links](#external-links)
17. [Conclusion](#conclusion)

# Generic info

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

Starting in 1.12, achievements, now referred to as "advancements", have become data-driven via modifiable JSON files rather than hard-coded. This allows map makers, modders, and server owners to modify and add advancements to their liking.

Advancements have a multitude of options, ranging from triggers based on the player's inventory to what biome they're in. Rewards can optionally be given, such as unlocking a recipe, providing an item, or providing experience. A new command, `/advancement`, has been introduced to supplement the files.

Advancements use the [JSON format](https://json.org) to store the advancement information in external files.

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="errors">Errors</a>

When an advancement fails for whatever reason, you can find failure messages in the Game Output window in the log files.

### Game Output window

To open the Game Output window when launching the game, you must enable it in the "Settings" tab of the launcher.

![](https://www.skylinerw.com/images/advancements/errors_output_1.png)

This window will show the error and stacktrace explaining the issue.

![](https://www.skylinerw.com/images/advancements/errors_output_2.png)

### Log files

If you no longer have the Game Output window open, you can check the output log. You can quickly access the Minecraft program files in the launcher by navigating to **Launch Options -> + Add New**, and clicking the "Go to folder" button:

![](https://www.skylinerw.com/images/advancements/errors_output_3.png)

From there, navigate to the `/logs/latest.log` file, which will contain the stacktrace. In this case, the error is stating that the advancement has a display, but is missing a description:

```
[07:02:59] [Server thread/ERROR]: Parsing error loading custom advancement custom:example
com.google.gson.JsonSyntaxException: Missing description
    at qe.a(SourceFile:447) ~[17w15a.jar:?]
    at r.a(SourceFile:66) ~[17w15a.jar:?]
```

# Files

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-location">File location</a>

Advancements are saved as a datapack in the world folder to be distributed with the world itself, inside the `/datapacks/` folder. Here is an example structure within the world folder, where "New World" is the name of the world folder:

![](https://www.skylinerw.com/images/advancements/files_2.png)

For more information on setting up a data pack (including the creation of the required `pack.meta` file), see [here](https://minecraft.gamepedia.com/Data_pack).

### <a name="files-location-namespaces">Namespaces</a>

The root folder that you create within the `/New World/datapacks/<datapack name>/data/` folder will be referred to as the "namespace". This is what separates collections of advancements, such as for different mods or maps. **File/folder names must all be lowercase to circumvent issues with differing operating system file structures.**

From the image example above, `skylinerwadvancements`, `anothermod`, and `minecraft` are the namespaces.

The `minecraft` namespace in particular should **only** be used to overwrite default advancements, such as with the intent to prevent them from working. Place any new advancements in a new namespace and **not** within `minecraft`.

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-referencing">Referencing</a>

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

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="default-advancements">Replacing default advancements</a>

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

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-editing">Editing</a>

You will need to use a UTF-8 compliant text editor when creating and saving advancements that use unicode characters (such as the section symbol). By not encoding in UTF-8, special characters may not be saved correctly and Minecraft may fail to parse the data.

**File/folder names must all be lowercase to circumvent issues with differing operating system file structures.**

### Windows Notepad

Windows Notepad is capable of saving as UTF-8, though will default to ANSI. Instructions for saving a `.json` in UTF-8:

1. Click *Save* or *Save As*.
2. Set *"Save as type"* to *"All files (*.*)"*.
3. Name your file and append the name with `.json`.
4. Set *"Encoding"* to *"UTF-8"*.
5. Save.

Image example:

![](https://www.skylinerw.com/images/advancements/editing_1.png)

### Notepad++

[Notepad++](https://notepad-plus-plus.org) is a great alternative to using plain Notepad. Instructions for saving a `.json` in UTF-8:

1. Change the encoding to either *"UTF-8"* or *"UTF-8 without BOM"* under the *"Encoding"* menu. See image below.
2. Click *Save* or *Save As*.
3. Set *"Save as type"* to *"All files (*.*)"*.
4. Name your file and append the name with `.json`.
5. Save.

Image example:

![](https://www.skylinerw.com/images/advancements/editing_2.png)

### Atom

[Atom](https://atom.io) has multi-platform support. Instructions for saving a `.json` in UTF-8:

1. Change the encoding to *"UTF-8"* under the *"Select Encoding"* menu. See image below.
2. Click *Save* or *Save As*.
3. Set *"Save as type"* to *"All files (*.*)"*.
4. Name your file and append the name with `.json`.
5. Save.

Image example:

![](https://www.skylinerw.com/images/advancements/editing_3.png)

# Data structures

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="structure">Complete JSON structure</a>

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

# Customizing advancements

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="criteria">Criteria</a>

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

**This image is currently outdated; last updated 18w03b.**

![](https://i.imgur.com/meB4V6V.png)

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="requirements">Requirements</a>

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

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display">Display</a>

The optional `display` object contains information about the display in the "Advancements" menu. If this object does not exist, the advancement will be hidden from the "Advancements" menu and popup notifications will not appear.

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-title-desc-icon">Title, description, & icon</a>

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

![](https://www.skylinerw.com/images/advancements/display_1.png)

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

![](https://www.skylinerw.com/images/advancements/display_4.png)

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

![](https://www.skylinerw.com/images/advancements/display_6.png)

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

![](https://www.skylinerw.com/images/advancements/display_7.png)

#### 3. `icon`

The `icon` object holds a required item ID of the item. This icon is shown in the "Advancements" menu, as well as in the popup notification when completing the advancement. The following icon specifies red wool:

```json
"icon": {
    "item": "minecraft:red_wool"
}
```

NBT data can be specified with the `nbt` string.

```json
"icon": {
    "item": "minecraft:red_wool",
    "nbt": "{test:1b}"
}
```

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-background">Background</a>

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

![](https://www.skylinerw.com/images/advancements/display_2.png)

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-frame">Frame</a>

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

![](https://www.skylinerw.com/images/advancements/display_3.png)

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

![](https://www.skylinerw.com/images/advancements/display_5.png)

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-toast">Show toast</a>

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

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-announce">Announce to chat</a>

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

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="display-hidden">Hidden</a>

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

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards">Rewards</a>

The `rewards` object specifies multiple types of rewards to provide the player upon completing the advancement.

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-recipes">`recipes`</a>

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

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-loot">`loot`</a>

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

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-experience">`experience`</a>

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

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="rewards-function">`function`</a>

This string specifies a single function file to run, with the value being the resource location to that function. The player will be considered the command sender and as such can be targeted with `@s` in the commands in the function. Each command will run in the specified order in the function. If the advancement is granted via command block, all of the listed commands will execute immediately, allowing other command blocks further in the chain to run based off of the results of the advancement's function being run (although you do not need to use advancements for this if focusing on functions).

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

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tree">Tree display</a>

When an advancement has a [display](#display), it will be shown in the "Advancements" menu under a relevant tab. [Root](#tree-root) advancements will be the "owner" of a tab, with new tabs appearing for each unique root:

![](https://www.skylinerw.com/images/advancements/tree_1.png)

Any advancements referring to the root will be its [children](#tree-branch) and are displayed in a branching tree:

![](https://www.skylinerw.com/images/advancements/tree_2.png)

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tree-root">Root</a>

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

### [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tree-branches">Branches</a>

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

![](https://www.skylinerw.com/images/advancements/tree_3.png)

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

![](https://www.skylinerw.com/images/advancements/tree_4.png)

Since you can have multiple branches per parent, complex trees can be created. The following image is a segment from the default "story" tab for vanilla Minecraft, showing complex connections between branches:

![](https://www.skylinerw.com/images/advancements/tree_5.png)

# Commands

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="command-advancement">Command: `/advancement`</a>

*Coming soon...*

# Conclusion

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="qa">Q&A</a>

*Coming soon...*

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="external-links">External links</a>

- [Generic JSON Validator](https://jsonlint.com)
- [Minecraft Wiki: Advancements](https://minecraft.gamepedia.com/Advancements)

Translations:

- [Chinese](https://pca006132.neocities.org/tutorials/advancement/advancement.html)

## [![Top](https://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conclusion">Conclusion</a>

If there is information that needs to be added, corrected, or clarified, please create a new issue!
