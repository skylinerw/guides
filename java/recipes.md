# Custom Recipes

*Last updated: 18w08a*

***

# Table of contents

## Generic info

<a name="table-of-contents"/>

1. [Intro](#intro)
2. [JSON structure](#structure)
3. [Shared: item element](#generic-item)
    1. [List of objects](#generic-item-list)

## Files

4. [Location](#files-location)
    1. [Namespaces](#files-location-namespaces)
5. [Referencing](#files-referencing)
6. [Replacing default recipes](#default-recipes)
    1. [List of default recipes](https://minecraft.gamepedia.com/Crafting#Complete_recipe_list)
7. [Editing](#files-editing)

## Customizing recipes

8. [Type](#type)
9. [Groups](#groups)
10. [Shaped crafting](#shaped-crafting)
    1. [Patterns](#shaped-patterns)
    2. [Keys](#shaped-keys)
11. [Shapeless crafting](#shapeless-crafting)
    1. [Ingredients](#shapeless-ingredients)
12. [Smelting](#smelting)
    1. [Ingredient](#smelting-ingredient)
    2. [Experience](#smelting-experience)
    3. [Cooking time](#smelting-cookingtime)
13. [Results](#results)
    1. [Crafting results](#results-crafting)
    2. [Smelting results](#results-smelting)

## Commands

14. [Command: `/recipe`](#command-recipe)

## Conclusion

15. [Q&A](#qa)
16. [External links](#external-links)
17. [Conclusion](#conclusion)

# Generic info

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

As of 1.12, recipes have become data-driven via external JSON files. This allows map makers, modders, and server owners to modify and add recipes to their liking.

Recipes can be shaped or shapeless, producing a specific item. As of 18w08a, only crafting table recipes and smelting recipes are customizable. A new command, [`/recipe`](#command-recipe), has been introduced to supplement the files. Advancements may check if a player [unlocked a recipe](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md#recipe_unlocked) or they can [grant recipes](https://github.com/skylinerw/guides/blob/master/java/advancements.md#rewards-recipes) to players.

Recipes use the [JSON format](http://json.org/) to store the recipe in external files.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="structure">JSON structure</a>

The following is a list of all possible keys for recipes.

```json
{
    "type": "crafting_shaped",
    "group": "group name",
    "pattern": [
        "## ",
        "## ",
        "((("
    ],
    "key": {
        "#": {
            "item": "minecraft:stone",
            "tag": "minecraft:grass"
        },
        "(": [
            {
                "item": "minecraft:stone",
                "tag": "minecraft:grass"
            }
        ]
    },
    "ingredients": [
        {
            "item": "minecraft:stone",
            "tag": "minecraft:grass"
        },
        [
            {
                "item": "minecraft:stone",
                "tag": "minecraft:grass"
            }
        ]
    ],
    "ingredient": [
        {
            "item": "minecraft:stone",
            "tag": "minecraft:grass"
        },
        [
            {
                "item": "minecraft:stone",
                "tag": "minecraft:grass"
            }
        ]
    ],
    "experience": 0.0,
    "cookingtime": 200,
    "result": {
        "item": "minecraft:stone",
        "count": 1
    },
    "result": "minecraft:stone"
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-item">Shared: item element</a>

An item element contains a handful of data to either compare with an incoming object or to create an outgoing object. The context of the values may change based on the feature using the item object, and some may not be available depending on that context.

**1. "item"**

The `item` string specifies a base item ID. `item` and `tag` are mutually exclusive. The following either looks for or creates the "minecraft:redstone" item.

```json
"item_object": {
    "item": "minecraft:redstone"
}
```

**2. "tag"**

The `tag` string specifies the resource location to an ID group, minus the designating `#` character. These groups are a list of item IDs that the incoming item can match any one of. For a list of default item groups, see [Minecraft Wiki: Tag](https://minecraft.gamepedia.com/Tag#Items). Adding custom groups requires the use of [data packs](https://minecraft.gamepedia.com/Data_pack). `item` and `tag` are mutually exclusive. This **cannot** be used in a [result](#results). The following looks for any item listed in the "#minecraft:wool" group.

```json
"item_object": {
    "tag": "minecraft:wool"
}
```

**3. "count"**

The optional `count` number specifies the amount of items in the stack, defaulting to 1 when not specified. This **cannot** be used in a [key](#shaped-keys), [crafting ingredient](#shapeless-ingredients), or [smelting ingredient](#smelting-ingredient), only in a [crafting result](#results-crafting). The following creates an item with a stacksize of 16.

```json
"item_object": {
    "item": "minecraft:stick",
    "count": 16
}
```
## <a name="generic-item-list">List of objects</a>

As an alternative to using tag groups, an item element can also be a list of item objects, but **only** for [keys](#shaped-keys), [crafting ingredients](#shapeless-ingredients), or [smelting ingredients](#smelting-ingredient) (not any [results](#results)). For example, the following will allow an ingredient to either be a stone **or** a stick.

```json
[
    {
        "item": "minecraft:stone"
    },
    {
        "item": "minecraft:stick"
    }
]
```

A shaped recipe example, where the items to find will be a stone, and either a stick **or** a white wool.

```json
{
    "type": "crafting_shaped",
    "pattern": [
        "%%%",
        "GGG"
    ],
    "key": {
        "%": {
            "item": "minecraft:stone"
        },
        "G": [
            {
                "item": "minecraft:stick"
            },
            {
                "item": "minecraft:white_wool"
            }
        ]
    },
    "result": {
        "item": "minecraft:redstone"
    }
}
```

A shapeless recipe example, where the items required must be a stone, and either a stick **or** a white wool.

```json
{
    "type": "crafting_shapeless",
    "ingredients": [
        {
            "item": "minecraft:stone"
        },
        [
            {
                "item": "minecraft:stick"
            },
            {
                "item": "minecraft:white_wool"
            }
        ]
    ],
    "result": {
        "item": "minecraft:redstone"
    }
}
```

# Files

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-location">File location</a>

Recipes are saved as a datapack in the world folder to be distributed with the world itself, inside the `/datapacks/` folder. Here is an example structure within the world folder, where "New World" is the name of the world folder:

![](http://skylinerw.com/images/recipes/recipe_files_1.png)

For more information on setting up a data pack (including the creation of the required `pack.meta` file), see [here](https://minecraft.gamepedia.com/Data_pack).

### <a name="files-location-namespaces">Namespaces</a>

The root folder that you create within the `/New World/datapacks/<datapack name>/data/` folder will be referred to as the "namespace". This is what separates collections of recipes, such as for different mods or maps. **File/folder names must all be lowercase to circumvent issues with differing operating system file structures.**

From the image example above, `skylinerwrecipes`, `anothermod`, and `minecraft` are the namespaces.

The `minecraft` namespace in particular should **only** be used to overwrite default recipes, such as changing the crafting output. Place any new recipes in a new namespace and **not** within `minecraft`.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-referencing">Referencing</a>

When referencing a recipe, you must follow the following format **excluding the `.json` extension**:

```
[namespace]:[filepath/to/recipe]
```

Example, targeting the file `/New World/datapacks/<datapack name>/data/anothermod/recipes/stuff/bowl.json`:

```
anothermod:stuff/bowl
```

By excluding the namespace, it will automatically assume it's meant to be `minecraft`:

```
acacia_button = minecraft:acacia_button
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="default-recipes">Replacing default recipes</a>

You can create a recipe within the `minecraft` namespace with the same name as a default recipe. Anything that uses the default recipe will then use your custom one.

For example, given the filepath `/New World/datapacks/<datapack name>/minecraft/recipes/acacia_button.json`, the following recipe will replace the default recipe that creates an acacia button, and instead provides dirt:

```json
{
    "type": "crafting_shapeless",
    "group": "wooden_button",
    "ingredients": [
        {
            "item": "minecraft:acacia_planks"
        }
    ],
    "result": {
        "item": "minecraft:dirt"
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="files-editing">Editing</a>

You will need to use a UTF-8 compliant text editor when creating and saving recipes that use unicode characters (such as the section symbol). By not encoding in UTF-8, special characters may not be saved correctly and Minecraft may fail to parse the data.

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

# Customizing recipes

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="type">Type</a>

A recipe **must** have a type of crafting layout, specified in the `type` string. The value can either be "crafting_shaped" for [shaped crafting](#shaped-crafting), "crafting_shapeless" for [shapeless crafting](#shapeless-crafting), or "smelting" for [smelting](#smelting).

The following recipe makes use of shaped crafting.

```json
{
    "type": "crafting_shaped",
    "pattern": [
        "##",
        "##",
        "##"
    ],
    "key": {
        "#": {
            "item": "minecraft:redstone"
        }
    },
    "result": {
        "item": "minecraft:stone"
    }
}
```

The following recipe makes use of shapeless crafting.

```json
{
    "type": "crafting_shapeless",
    "ingredients": [
        {
            "item": "minecraft:redstone"
        }
    ],
    "result": {
        "item": "minecraft:stone"
    }
}
```

The following recipe makes use of smelting.

```json
{
    "type": "smelting",
    "ingredient": {
        "item": "minecraft:redstone"
    },
    "result": "minecraft:stone"
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="groups">Groups</a>

A recipe can optionally have a recipe group it belongs to within the recipe helper interface, specified by the `group` string. The value of this string can be anything. Any recipes that have the same group name specified will be shown together in the recipe helper. The intention is to keep similar items within the same category, such as all boats:

![Recipe book group](http://skylinerw.com/images/recipes/recipe_group.png)

The following recipes belong to a group called "stuff" and will be grouped together in the recipe helper interface:

```json
{
    "type": "crafting_shapeless",
    "group": "stuff",
    "ingredients": [
        {
            "item": "minecraft:sand"
        }
    ],
    "result": {
        "item": "minecraft:diamond"
    }
}

{
    "type": "crafting_shapeless",
    "group": "stuff",
    "ingredients": [
        {
            "item": "minecraft:dirt"
        }
    ],
    "result": {
        "item": "minecraft:diamond"
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shaped-crafting">Shaped crafting</a>

Shaped crafting involves placing items into the crafting window in a specific order. A [pattern](#shaped-patterns) specifies the slots that items must appear in using arbitrary placeholder characters, while [keys](#shaped-keys) define what items those placeholders are looking for. Patterns and keys are both required with shaped recipes.

The following sets two placeholders in the pattern, being "#" and "^", while the keys define "#" as redstone dust and "^" as an iron block.

```json
{
    "type": "crafting_shaped",
    "pattern": [
        "###",
        "^^^",
        "###"
    ],
    "key": {
        "#": {
            "item": "minecraft:redstone"
        },
        "^": {
            "item": "minecraft:iron_block"
        }
    },
    "result": {
        "item": "minecraft:gold_block"
    }
}
```

Image example of how it is used in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_shaped.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shaped-patterns">Patterns</a>

A pattern **must** be specified via the `pattern` list. The list must contain between 1 and 3 strings, with each string representing a row in the crafting grid. Within each string there must be between 1 and 3 placeholder characters of your choosing, with each character representing a column. A space character indicates an empty slot in the crafting window, where no items are allowed to appear. The shape will automatically adapt to larger crafting grids, such that a 2x2 recipe will work in any corner of a 3x3 crafting grid.

For example, the following pattern uses only 1 row and all 3 columns, with an empty slot between the two items:

```json
"pattern": [
    "# #"
]
```

Which could look like any of the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_pattern_1.png)

All strings must contain the same number of characters. The following pattern is **invalid** due to the uneven rows.

```json
"pattern": [
    "##",
    "#"
]
```

The fix would be to specify an empty slot where the rows are uneven.

```json
"pattern": [
    "##",
    "# "
]
```

The fixed pattern could look like any of the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_pattern_2.png)

***

A pattern can have up to 9 unique placeholder characters. These characters can be anything (except the reserved space character), provided there is a corresponding [key](#shaped-keys) to go with them, although that doesn't mean that the items defined by those keys have to be unique. The following recipe makes use of 9 placeholders.

```json
"pattern": [
    "!@#",
    "$%^",
    "&*("
]
```

You can also use a single row and column to create a recipe where the item may be placed anywhere in the crafting grid, although you should use [shapeless crafting](#shapeless-crafting) instead as it is for unordered recipes.

```json
"pattern": [
    "#"
]
```

Which could look like the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_ingredients_4.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shaped-keys">Keys</a>

A set of keys **must** be specified via the `key` object. This object contains multiple [item elements](#generic-item), whose key names must be equal to the unique characters specified in the [pattern](#shaped-patterns). The key name can only be 1 character long and cannot be the reserved space character.

The following pattern set uses "H" and "?" as the placeholders, while the key defines what items those placeholders will be. The "H" represents a lever, while the "?" represents a bone.

```json
"pattern": [
    "H?H",
    "?H?",
    "H?H"
],
"key": {
    "H": {
        "item": "minecraft:lever"
    },
    "?": {
        "item": "minecraft:bone"
    }
}
```

Which would look like the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_key_1.png)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shapeless-crafting">Shapeless crafting</a>

Shapeless crafting involves placing items anywhere into the crafting window in no particular order. A list of [ingredients](#shapeless-ingredients) specifies the items that make up the recipe, which is required for shapeless recipes.

The following recipe will allow placing a diamond and a nether star anywhere in the crafting grid to receive a stick in return.

```json
{
    "type": "crafting_shapeless",
    "ingredients": [
        {
            "item": "minecraft:diamond"
        },
        {
            "item": "minecraft:nether_star"
        }
    ],
    "result": {
        "item": "minecraft:stick"
    }
}
```

Image example of how it is used in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_shapeless.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shapeless-ingredients">Ingredients</a>

A set of required items **must** be specified via the `ingredients` list. This list contains [item elements](#generic-item); if the player does not provide all items in this list, the recipe will not work. There can only be 9 items elements listed at the root of the `ingredients` list.

The following will only work if the player provides all 5 items, which also means they cannot use the standard inventory crafting grid (2x2) to craft the item as there is not enough room.

```json
"ingredients": [
    {
        "item": "minecraft:redstone"
    },
    {
        "item": "minecraft:diamond"
    },
    {
        "item": "minecraft:iron_block"
    },
    {
        "item": "minecraft:gold_block"
    },
    {
        "item": "minecraft:diamond_block"
    }
]
```

Which could look like the following in the crafting window, plus many more combinations:

![](http://skylinerw.com/images/recipes/recipe_ingredients_1.png)

You can use the same item multiple times, in which the player must provide all instances of that item.

```json
"ingredients": [
    {
        "item": "minecraft:golden_carrot"
    },
    {
        "item": "minecraft:golden_carrot"
    }
]
```

Which could look like the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_ingredients_3.png)

You can also require just a single item, and that item can be placed anywhere in the crafting window.

```json
"ingredients": [
    {
        "item": "minecraft:torch"
    }
]
```

Which could look like the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_ingredients_4.png)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="smelting">Smelting</a>

Smelting involves placing an item into a furnace to smelt into a different item. The recipe takes an [ingredient](#smelting-ingredient) to produce a singular [result](#results-smelting). An optional [experience reward](#smelting-experience) and [cook time](#smelting-cookingtime) may be defined.

The following recipe will turn a piece of dirt into a diamond, providing an approximated 40% chance of 1 experience per item cooked and cooking each item in 20 ticks (1 second).

```json
{
    "type": "smelting",
    "ingredient": {
        "item": "minecraft:dirt"
    },
    "result": "minecraft:diamond",
    "experience": 0.4,
    "cookingtime": 20
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="smelting-ingredient">Ingredient</a>

A single required item **must** be specified via the `ingredient` [item element](#generic-item).

The following will allow the player to smelt either dirt or grass into diamond, or anything from the "minecraft:wool" tag group.

```json
{
    "type": "smelting",
    "ingredient": [
        {
            "item": "minecraft:dirt"
        },
        {
            "item": "minecraft:grass"
        },
        {
            "tag": "minecraft:wool"
        }
    ],
    "result": "minecraft:diamond"
}
```

Or to simply select one item, you would define the item element as an object.

```json
{
    "type": "smelting",
    "ingredient": {
        "item": "minecraft:dirt"
    },
    "result": "minecraft:diamond"
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="smelting-experience">Experience</a>

**Note: a bug currently causes the modifier to be based on the item currently in the smelting slot rather than the output slot: [MC-125961](https://bugs.mojang.com/browse/MC-125961)**

An optional `experience` float **modifies** rate that experience will be provided for smelting, being a value between 0.0 and 1.0 (with 1.0 signifying a 100% experience rate). When not specified, defaults to 0.0. The rate of experience provided will be based on `rate = modifier * items removed from furnace`, where "modifier" is the value from `experience`. If the result is not a whole number, there is a `rate - floor(rate)` chance that the experience given will be `ceil(rate)`, otherwise the experience given is `floor(rate)`.

For example, if 27 items were removed from the furnace while `experience` set to 0.25, the resulting experience rate is `0.25 * 27 = 6.75`. As this is not a whole number, there is a 75% chance (`6.75 - 6.0 = 0.75`) to round up and provide 7 experience points, otherwise it will simply be floored to 6 experience points.

This can be represented logically as:

```java
float modifier = 0.25; // The specified "experience" value.
int itemsRemoved = 27; // The number of items removed from the furnace output slot.
int experienceProvided = itemsRemoved; // Experience provided is based on items removed.

// If "experience" is specified as greater than 0 and less than 1...

if (modifier > 0.0 && modifier < 1.0) {

    float rate = modifier * itemsRemoved; // Result: 6.75
    experienceProvided = floor(rate); // Result: 6.0

    // If the rate is not whole (6.0 != 6.75), and with a 75% chance to continue...

    if (experienceProvided != rate && Math.random() < rate - experienceProvided) {
        
        // Experience provided will instead be the floored rate plus one.
        
        experienceProvided = ceil(rate); // Result: 7.0
    }
}

// Return experience, being one of the following depending on initial values: itemsRemoved, floor(rate), or ceil(rate).

return experienceProvided;
```

As another example, if `experience` is set to 1.0 (100% experience rate) and 50 items were removed, then the player receives 50 experience points.

As for a recipe example, where the rate modifier is set to 0.5 (i.e. the experience provided will be equal to roughly half of the number of items removed from the output slot of the furnace):

```json
{
    "type": "smelting",
    "ingredient": {
        "item": "minecraft:dirt"
    },
    "result": "minecraft:diamond",
    "experience": 0.5
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="smelting-cookingtime">Cooking time</a>

The length of time (in ticks) that it takes to smelt an item can be specified with the `cookingtime` integer. When not specified, defaults to 200.

The following will smelt an item in 10 ticks (0.5 seconds).

```json
{
    "type": "smelting",
    "ingredient": {
        "item": "minecraft:stone"
    },
    "result": "minecraft:grass",
    "cookingtime": 20
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="results">Results</a>

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="results-crafting">Crafting results</a>

A crafting recipe **must** have a resulting item that is crafted, specified in the `result` [item element](#generic-item).

For example, if the following shaped recipe is fulfilled, the player will receive a new anvil.

```json
{
    "type": "crafting_shaped",
    "pattern": [
        "#%#",
        " # "
    ],
    "key": {
        "%": {
            "item": "minecraft:damaged_anvil"
        },
        "#": {
            "item": "minecraft:iron_ingot"
        }
    },
    "result": {
        "item": "minecraft:anvil"
    }
}
```

![](http://skylinerw.com/images/recipes/recipe_result_1.png)

Unlike in `key` and `ingredients` item elements, the `count` key may be specified in `result` to provide the player with multiple items. The following shapeless recipe will create 4 sticks when the recipe is fulfilled.

```json
{
    "type": "crafting_shapeless",
    "ingredients": [
        {
            "item": "minecraft:flint"
        },
        {
            "item": "minecraft:wheat_seeds"
        }
    ],
    "result": {
        "item": "minecraft:stick",
        "count": 4
    }
}
```

![](http://skylinerw.com/images/recipes/recipe_result_2.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="results-smelting">Smelting results</a>

A smelting recipe **must** have a resulting item, specified as an item ID in the `result` string.

For example, if the following smelting recipe will provide glass when any block in the "minecraft:wool" tag group is smelted.

```json
{
    "type": "smelting",
    "ingredient": {
        "tag": "minecraft:wool"
    },
    "result": "minecraft:glass"
}
```

# Commands

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="command-recipe">Command: /recipe</a>

*Coming soon...*

# Conclusion

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="qa">Q&A</a>

> Could custom recipes be implemented prior to 1.13?

Not without modifying the JAR.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="external-links">External links</a>

- [Generic JSON validator](http://jsonlint.com)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conclusion">Conclusion</a>

If there is information that needs to be added, corrected, or clarified, please create a new issue!
