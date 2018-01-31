# Custom Recipes

*Last updated: 18w05a*

***

# Table of contents

## Generic info

<a name="table-of-contents"/>

1. [Intro](#intro)
2. [JSON structure](#structure)
3. [Shared: item element](#generic-item)
   1. [List of objects](#generic-item-list)

## Files

3. [Location](#files-location)
    1. [Namespaces](#files-location-namespaces)
4. [Referencing](#files-referencing)
5. [Replacing default recipes](#default-recipes)
    1. [List of default recipes](https://minecraft.gamepedia.com/Crafting#Complete_recipe_list)
6. [Editing](#files-editing)

## Customizing recipes

4. [Type](#type)
5. [Groups](#groups)
6. [Shaped crafting](#shaped-crafting)
   1. [Patterns](#shaped-patterns)
   2. [Keys](#shaped-keys)
7. [Shapeless crafting](#shapeless-crafting)
   1. [Ingredients](#shapeless-ingredients)
8. [Results](#results)

## Commands

9. [Command: `/recipe`](#command-recipe)

## Conclusion

10. [Q&A](#qa)
11. [External links](#external-links)
12. [Conclusion](#conclusion)

# Generic info

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

As of 1.12, recipes have become data-driven via external JSON files. This allows map makers, modders, and server owners to modify and add recipes to their liking.

Recipes can be shaped or shapeless, producing a specific item. As of 18w05a, only crafting table recipes are customizable. A new command, [`/recipe`](#command-recipe), has been introduced to supplement the files. Advancements may check if a player [unlocked a recipe](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md#recipe_unlocked), or [grant recipes](https://github.com/skylinerw/guides/blob/master/java/advancements.md#rewards-recipes) to players.

Recipes use the [JSON format](http://json.org/) to store the recipe in external files.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="structure">JSON structure</a>

The following is a list of all possible keys for recipes.

```json
{
    "type": "crafting_shaped",
    "group": "group name",
    "pattern": [
        "##",
        "##",
        "((("
    ],
    "key": {
        "#": {
            "item": "minecraft:stone",
            "tag": "minecraft:planks"
        },
        "(": [
            {
                "item": "minecraft:stone",
                "tag": "minecraft:planks"
            }
        ]
    },
    "ingredients": [
        {
            "item": "minecraft:stone",
            "tag": "minecraft:planks"
        },
        [
            {
                "item": "minecraft:stone",
                "tag": "minecraft:planks"
            }
        ]
    ],
    "result": {
        "item": "minecraft:stone",
        "count": 1
    }
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

The `tag` string specifies the resource location to an ID group, minus the designating `#` character. These groups are a list of item IDs that the incoming item can match any one of. For a list of default item groups, see [Minecraft Wiki: Tag](https://minecraft.gamepedia.com/Tag#Items). Adding custom groups requires the use of [data packs](https://minecraft.gamepedia.com/Data_pack). `item` and `tag` are mutually exclusive. This **cannot** be used in a [result](#result). The following looks for any item listed in the "#minecraft:wool" group.

```json
"item_object": {
    "tag": "minecraft:wool"
}
```

**3. "count"**

The optional `count` number specifies the amount of items in the stack, defaulting to 1 when not specified. This **cannot** be used in a [key](#shaped-keys) or [ingredient](#shapeless-ingredients), only in a [result](#result). The following creates an item with a stacksize of 16.

```json
"item_object": {
    "item": "minecraft:stick",
    "count": 16
}
```
## <a name="generic-item-list">List of objects</a>

As a shortcut to using tag groups, an item element can also be a list of item objects, but **only** for [keys](#shaped-keys) and [ingredients](#shapeless-ingredients) (not a [result](#result)). For example, the following will allow crafting with either a stone **or** a stick.

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

A recipe **must** have a type of crafting layout, specified in the `type` string. The value can either be "crafting_shaped" for [shaped crafting](#shaped-crafting) or "crafting_shapeless" for [shapeless crafting](#shapeless-crafting).

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

While the following recipe makes use of shapeless crafting.

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

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="groups">Groups</a>

A recipe can optionally have a recipe group it belongs to within the crafting helper interface, specified by the `group` string. The value of this string can be anything. Any recipes that have the same group name specified will be shown together in the crafting helper. The intention is to keep similar items within the same category, such as all boats:

![Recipe book group](http://skylinerw.com/images/recipes/recipe_group.png)

The following recipes belong to a group called "stuff" and will be grouped together in the crafting helper interface:

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
        "##",
        "^^^",
        "##"
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

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="results">Results</a>

A recipe **must** have the resulting item that is crafted, specified in the `result` [item element](#generic-item).

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
