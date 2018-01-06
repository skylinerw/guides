# Custom Recipes

*Last updated: 17w16a*

***

## Table of contents

#### Generic info

<a name="table-of-contents"/>

1. [Intro](#intro)
2. [JSON structure](#structure)
3. [Shared: item object](#generic-item)

#### Customizing recipes

4. [Type](#type)
5. [Groups](#groups)
6. [Shaped crafting](#shaped-crafting)
   1. [Patterns](#shaped-patterns)
   2. [Keys](#shaped-keys)
7. [Shapeless crafting](#shapeless-crafting)
   1. [Ingredients](#shapeless-ingredients)
8. [Results](#results)

#### Commands

9. [Command: `/recipe`](#command-recipe)

#### Conclusion

10. [Q&A](#qa)
11. [External links](#external-links)
12. [Conclusion](#conclusion)

## Generic info

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

As of 1.12, recipes have become data-driven via external JSON files. This allows map makers, modders, and server owners to modify and add recipes to their liking.

Recipes can be shaped or shapeless, producing a specific item. As of 17w13b, only crafting table recipes are customizable. A new command, [`/recipe`](#command-recipe), has been introduced to supplement the files.

Recipes use the [JSON format](http://json.org/) to store the recipe in external files.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="structure">JSON structure</a>

The following is a list of all possible keys for recipes.

```json
{
    "type": "crafting_shaped",
    "group": "identifier",
    "pattern": [
        "###",
        "###",
        "###"
    ],
    "key": {
        "#": {
            "item": "minecraft:stone",
            "data": 0,
            "count": 1
        }
    },
    "ingredients": [
        {
            "item": "minecraft:stone",
            "data": 0,
            "count": 1
        }
    ],
    "result": {
        "item": "minecraft:stone",
        "data": 0,
        "count": 1
    }
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="generic-item">Shared: item object</a>

An item object contains a handful of data to either compare with an incoming object or to create an outgoing object. The context of the values may change based on the feature using the item object, and some may not be available depending on that context.

**1. "item"**

The required `item` string specifies a base item ID. The following either looks for or creates the "minecraft:redstone" item.

```json
"item_object": {
    "item": "minecraft:redstone"
}
```

**2. "data"**

The optional `data` number specifies a metadata of the item. The following either looks for or creates a polished granite block.

```json
"item_object": {
    "item": "minecraft:stone",
    "data": 2
}
```

When used in a [key](#shaped-keys) or [ingredient](#shapeless-ingredients), not specifying this tag or giving it a value of 32767 will indicate that the metadata value can be anything. When used in a [result](#result), it will set the item's metadata to the value specified, defaulting to 0 when not specified.

**3. "count"**

The optional `count` number specifies the number of items in the stack, defaulting to 1 when not specified. This **cannot** be used in a [key](#shaped-keys) or [ingredient](#shapeless-ingredients), only in a [result](#result). The following creates an item with a stacksize of 16.

```json
"item_object": {
    "item": "minecraft:stick",
    "count": 16
}
```

## Customizing recipes

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="type">Type</a>

A recipe **must** have a type of crafting layout, specified in the `type` string. The value can either be "crafting_shaped" for [shaped crafting](#shaped-crafting) or "crafting_shapeless" for [shapeless crafting](#shapeless-crafting).

The following recipe makes use of shaped crafting.

```json
{
    "type": "crafting_shaped",
    "pattern": [
        "###",
        "###",
        "###"
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

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="groups">Groups</a>

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

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shaped-crafting">Shaped crafting</a>

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

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shaped-patterns">Patterns</a>

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
    "###",
    "##"
]
```

The fix would be to specify an empty slot where the rows are uneven.

```json
"pattern": [
    "###",
    "## "
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

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shaped-keys">Keys</a>

A set of keys **must** be specified via the `key` object. This object contains multiple [item objects](#generic-item), whose key names must be equal to the unique characters specified in the [pattern](#shaped-patterns). The key name can only be 1 character long and cannot be the reserved space character.

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

***

If the `data` key is either not specified or has a value of 32767, the metadata value of the incoming item will be ignored. The following pattern set will accept stone, diamond swords, and wool of any kind.

```json
"pattern": [
    "###",
    "555",
    "TTT"
],
"key": {
    "#": {
        "item": "minecraft:stone"
    },
    "5": {
        "item": "minecraft:diamond_sword"
    },
    "T": {
        "item": "minecraft:wool",
        "data": 32767
    }
}
```

Which could look like the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_key_2.png)

To work around that, you would specify the metadata value. The following pattern set ensures that the incoming diamond swords are brand new.

```json
"pattern": [
    "##"
],
"key": {
    "#": {
        "item": "minecraft:diamond_sword",
        "data": 0
    }
}
```

![](http://skylinerw.com/images/recipes/recipe_key_3.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shapeless-crafting">Shapeless crafting</a>

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

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shapeless-ingredients">Ingredients</a>

A set of required items **must** be specified via the `ingredients` list. This list contains [item objects](#generic-item); if the player does not provide all items in this list, the recipe will not work. There is no limit to the number of items, though be aware that in vanilla there can only be 9 total items to provide in a crafting table.

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

***

If the `data` key is either not specified or has a value of 32767, the metadata value of the incoming item will be ignored. The following will accept stone, diamond swords, and wool of any kind.

```json
"ingredients": [
    {
        "item": "minecraft:stone"
    },
    {
        "item": "minecraft:diamond_sword"
    },
    {
        "item": "minecraft:wool",
        "data": 32767
    }
]
```

Which could look like the following in the crafting window:

![](http://skylinerw.com/images/recipes/recipe_ingredients_2.png)

To work around that, you would specify the metadata value. The following ensures that the incoming diamond swords are brand new.

```json
"ingredients": [
    {
        "item": "minecraft:diamond_sword",
        "data": 0
    }
]
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="results">Results</a>

A recipe **must** have the resulting item that is crafted, specified in the `result` [item object](#generic-item). Unlike in `key` and `ingredients` item objects, the `data` key will default to 0 when not specified.

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
            "item": "minecraft:anvil",
            "data": 2
        },
        "#": {
            "item": "minecraft:iron_ingot"
        }
    },
    "result": {
        "item": "minecraft:anvil",
        "data": 0
    }
}
```

![](http://skylinerw.com/images/recipes/recipe_result_1.png)

Unlike in `key` and `ingredients` item objects, the `count` key may be specified in `result` to provide the player with multiple items. The following shapeless recipe will create 4 sticks when the recipe is fulfilled.

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

## Commands

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="command-recipe">Command: /recipe</a>

*Coming soon...*

## Conclusion

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="qa">Q&A</a>

> Can custom recipes be used in 17w13b?

Not quite. The JAR must be modified directly, but in the future we will be able to modify recipes on a per-world basis, same as loot tables and advancements. This thread was created to prepare for that, so be aware that information in this thread is highly likely to become outdated once custom crafting becomes available without modding. Once it's possible, I will add instructions for file placement.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="external-links">External links</a>

- [Generic JSON validator](http://jsonlint.com)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conclusion">Conclusion</a>

If there is information that needs to be added, corrected, or clarified, please create a new issue!
