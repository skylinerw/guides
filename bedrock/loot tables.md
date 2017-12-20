# Loot Tables

***

# Table of contents

### Generic info

1. [Intro](#intro)
2. [Editing](#editing)

### Pools

3. [Tiers](#tiers)
    1. [Initial range](#tiers-initial-range)
    2. [Bonus rolls & bonus chance](#tiers-bonus-rolls)

### Entries

4. [Nested pools](#entry-pools)

### Functions

5. [`minecraft:enchant_book_for_trading`](#function-enchant_book_for_trading)
6. [`minecraft:enchant_random_gear`](#function-enchant_random_gear)
7. [`minecraft:enchant_randomly`](#function-enchant_random_gear)
8. [`minecraft:exploration_map`](#function-exploration_map)
9. [`minecraft:looting_enchant`](#function-looting_enchant)
10. [`minecraft:random_aux_value`](#function-random_aux_value)
11. [`minecraft:set_data_from_color_index`](#function-set_data_from_color_index)
12. [`minecraft:set_nbt`](#function-set_nbt)
13. [`minecraft:set_attributes`](#function-set_attributes)

### Conditions

14. [`minecraft:killed_by_player`](#condition-killed_by_player)
15. [`minecraft:random_difficulty_chance`](#condition-random_difficulty_chance)
16. [`minecraft:random_regional_difficulty_chance`](#condition-random_difficulty_chance)
17. [`minecraft:entity_scores`](#condition-entity_scores)

### Conclusion

18. [External links](#external-links)
19. [Conclusion](#conclusion)

# Table of contents

# Generic info

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

Loot tables are a data-driven method of defining a set of items to provide to relevant context, such as mob loot or mob equipment.

This guide for the Bedrock codebase (includes Pocket Edition & Windows 10 Edition) is an extension of my [Java Edition guide](https://github.com/skylinerw/guides/blob/master/java/loot%20tables.md). Many details are the same, so this guide will instead cover the differences between versions. Please view the other guide for more extensive details.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="editing">Editing</a>

Please view the [Creating Behavior Packs](https://minecraft.gamepedia.com/Tutorials/Creating_behavior_packs) wiki page for details on setting up behavior packs, as well as recommended programs for editing.

# Pools

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tiers">Tiers</a>

Tiers are an alternative method of selecting entries as compared to basic rolls. Rather than a random entry being selected from within the list, each entry is a tier, with the tier increasing for each entry specified. The entry to select is dependent on tier options provided. The following table indicates that "stone" is tier 1, "sand" is tier 2, and "grass" is tier 3.

```json
{
    "pools": [
        {
            "tiers": {
                
            },
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone"
                },
                {
                    "type": "item",
                    "name": "minecraft:sand"
                },
                {
                    "type": "item",
                    "name": "minecraft:grass"
                }
            ]
        }
    ]
}
```

Without using tiers, the specified order is irrelevant and a random entry is selected as usual.

When the `tiers` object is specified, the `rolls` option for pools is ignored, rendering `weight` and `quality` for entries useless. For example, given the following table, **rolls, weight, and qualities should be removed** because tiers have been specified:

```json
{
    "pools": [
        {
            "tiers": {
                
            },
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:lever",
                    "weight": 1,
                    "quality": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stone_button",
                    "weight": 1,
                    "quality": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "weight": 1,
                    "quality": 1
                },
                {
                    "type": "item",
                    "name": "minecraft:sand",
                    "weight": 1,
                    "quality": 1
                }
            ]
        }
    ]
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tiers-initial-range">Initial range</a>

An initial range of tiers can be defined as the starting position, intended to be mixed with [bonus rolls](#tiers-bonus-rolls), specified in the `initial_range` integer (must be 1 or higher). The value of the initial range will correspond to the highest tier that you want to have selected by default, regardless of bonus rolls. For example, given the following table:

```json
{
    "pools": [
        {
            "tiers": {
                "initial_range": 2
            },
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone"
                },
                {
                    "type": "item",
                    "name": "minecraft:sand"
                },
                {
                    "type": "item",
                    "name": "minecraft:grass"
                }
            ]
        }
    ]
}
```

"stone" is tier 1, "sand" is tier 2, and "grass" is tier 3. The initial range's value is 2, meaning all tiers between 1 and 2 ("stone" and "sand") will be selected by default. At that point, all of those selected tiers will have an equal chance of being selected. The basic formula for determining the chance of a tier within the range being selected would be:

```
chance = 1 / initial_range
```

As there are two tiers being selected, each has a 50% chance of being selected, while "grass" has a 0% chance due to having a tier too high for the initial range.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tiers-bonus-rolls">Bonus rolls & bonus chance</a>

When bonus rolls are specified, the tier selected from the initial range will increase depending on the values of `bonus_rolls` (the number of times to try moving up a tier, must be 1 or higher) and `bonus_chance` (the percent chance between 0.0 and 1.0 that a single bonus roll succeeds in moving up a tier). Given the following table:

```json
{
    "pools": [
        {
            "tiers": {
                "initial_range": 1,
                "bonus_rolls": 1,
                "bonus_chance": 1.0
            },
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone"
                },
                {
                    "type": "item",
                    "name": "minecraft:sand"
                },
                {
                    "type": "item",
                    "name": "minecraft:grass"
                }
            ]
        }
    ]
}
```

"stone" will always be the initial tier selected due to `initial_range` being 1. However, there is a 100% chance (via `bonus_chance`) of increasing the tier by 1 per bonus roll. In this case, the selected entry will always be "sand" because it is the next tier up. If `bonus_rolls` were 2, then "grass" will always be selected due to it being a 100% *per bonus roll* to move up 1 tier.

As another example, given the following table:

```json
{
    "pools": [
        {
            "tiers": {
                "initial_range": 1,
                "bonus_rolls": 2,
                "bonus_chance": 0.5
            },
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone"
                },
                {
                    "type": "item",
                    "name": "minecraft:sand"
                },
                {
                    "type": "item",
                    "name": "minecraft:grass"
                }
            ]
        }
    ]
}
```

"stone" is always the initial tier due to the initial range of 1. This time there is a 50% chance per bonus roll of increasing the tier by 1. Assuming the first of the 2 bonus rolls succeeds and causes tier 2 to now be selected, there is another 50% chance to use up the last bonus roll to move to tier 3. Thus there is a 25% chance of "stone" remaining as the selected tier after 2 bonus rolls, a 50% chance of "sand" both being bonus-rolled into and kept from there, and a 25% chance of "grass" being selected after both "stone" and "sand" are skipped by the 2 bonus rolls.

As a final example, given the following table:

```json
{
    "pools": [
        {
            "tiers": {
                "initial_range": 3,
                "bonus_rolls": 1,
                "bonus_chance": 0.25
            },
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone"
                },
                {
                    "type": "item",
                    "name": "minecraft:sand"
                },
                {
                    "type": "item",
                    "name": "minecraft:grass"
                }
            ]
        }
    ]
}
```

The initial range encompasses all available tiers, meaning each has a 33% chance of being initially selected. There is only 1 opportunity to move up a tier as defined by `bonus_rolls`, and that has a 25% chance of succeeding. Thus if "stone" is initially selected, there is a 25% chance that it moves up to "sand" instead. If "sand" is initially selected, it has a 25% chance of becoming "grass". And if "grass" is initially selected, then it has a 25% chance of moving to the next tier. However, there is not a next tier, which means no item will be provided in those cases.

# Entries

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entry-pools">Nested pools</a>

Unlike Java edition, entries are allowed to have pools of their own. However, this only works if the entry type is "item" or "loot_table", meaning "empty" will not work. The entry **must** successfully provide item(s), meaning you cannot use the `loot_tables/empty.json` table to stop the entry itself from providing items. Pools can continue to be nested within nested pools. The structure is simply a raw loot table:

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stone",
                    "pools": [
                        {
                            "rolls": 3,
                            "entries": [
                                {
                                    "type": "item",
                                    "name": "minecraft:sand"
                                },
                                {
                                    "type": "item",
                                    "name": "minecraft:grass"
                                }
                            ]
                        }
                    ]
                },
                {
                    "type": "item",
                    "name": "minecraft:stick"
                }
            ]
        }
    ]
}
```

The table will provide either "stone" or "stick" at a 50% split chance, but if "stone" is selected, it will also provide items from the nested pool (3 rolls, with "sand" and "grass" also being split at 50% chance).

# Functions

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-enchant_book_for_trading">`minecraft:enchant_book_for_trading`</a>

Enchants an item using a predetermined algorithm for enchanting items sold by villagers. Takes four modifiers with unknown usage: `base_cost`, `base_random_cost`, `per_level_cost`, and `per_level_random_cost`. These modifiers may actually do nothing in the context of a loot table, though the item will still be enchanted using whatever default values.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-enchant_random_gear">`minecraft:enchant_random_gear`</a>

Randomly enchants the item using a predetermined algorithm used for enchanting worn gear on mobs. Takes a `chance` modifier to manipulate the algorithm. Note that a "chance" modifier of 1.0 does not mean a 100% chance that gear will become enchanted. The following enchants the item based on these factors:

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
                            "function": "minecraft:enchant_random_gear",
                            "chance": 0.5
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-enchant_randomly">`minecraft:enchant_randomly`</a>

While this function exists in Java edition, there are a couple differences. Bedrock edition accepts a `treasure` boolean, allowing treasure enchantments to be toggled when applying random enchantments. However, unlike Java edition, there is no `enchantments` list that allows you to directly specify a list of enchantments to randomly select from.

The following allows you to enchant randomly but exclude treasure enchantments:

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
                            "function": "minecraft:enchant_randomly",
                            "treasure": false
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-exploration_map">`minecraft:exploration_map`</a>

Expected to transform a map into an exploration map (though I could not personally get it to work; might only be usable within villager trading context). Accepts a `destination` string, specifying which structure to locate, with valid values being "monument" and "mansion".

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-looting_enchant">`minecraft:looting_enchant`</a>

While both Java and Bedrock editions have this function, Bedrock does not have the `limit` integer to act as a cap on the number of new items provided through the Looting enchantment.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-random_aux_value">`minecraft:random_aux_value`</a>

Unknown. Takes a `values` array that crashes the game when inputting more than 1 element.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-set_data_from_color_index">`minecraft:set_data_from_color_index`</a>

Sets the data value of the item depending on internally-provided context. Because this context cannot be specified manually, this function is useless for all but a few cases, such as sheep wool color (killed or sheared). There are no options for this function. The following table provides stained hardened clay blocks with a data value corresponding to the color of a sheep's wool, provided this table is either used for sheep-killing or sheep-shearing:

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:stained_hardened_clay",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:set_data_from_color_index"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-set_nbt">`minecraft:set_nbt`</a>

The `minecraft:set_nbt` function from Java edition does not exist in the Bedrock edition.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="function-set_attributes">`minecraft:set_attributes`</a>

The `minecraft:set_attributes` function from Java edition does not exist in the Bedrock edition.

# Conditions

**NOTE:** Currently conditions may not use namespaces for Bedrock edition. Please keep that in mind when porting over any Java edition loot tables, which accepts namespaces for conditions.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="condition-killed_by_player">`minecraft:killed_by_player`</a>

While both Java and Bedrock editions have this condition, Bedrock does not have the `inverse` boolean to only allow the condition to succeed if the mob was not killed by a player.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="condition-random_difficulty_chance">`minecraft:random_difficulty_chance`</a>

This condition will be true based on a chance, modified by the difficulty setting. A base value specified in the `default_chance` double represents the chance that all unspecified difficulties have. Each difficulty, specified as `peaceful`, `easy`, `normal`, `hard`, can have their own chance that overwrites the default chance.

The following table has a 50% chance of succeeding on all unspecified difficulties, while easy and hard difficulties have a 10% and 100% chance respectively.

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
                    "conditions": [
                        {
                            "condition": "random_difficulty_chance",
                            "default_chance": 0.5,
                            "easy": 0.1,
                            "hard": 1.0
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="condition-random_regional_difficulty_chance">`minecraft:random_regional_difficulty_chance`</a>

This condition will be true based on a chance, related to a chunk's [regional difficulty](https://minecraft.gamepedia.com/Difficulty#Regional_difficulty). Only a `max_chance` double can be provided. Unsure exactly how chances are modified by regional difficulty.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="condition-entity_scores">`minecraft:entity_scores`</a>

The `minecraft:entity_scores` condition from Java edition does not exist in the Bedrock edition.

# Conclusion

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="external-links">External links</a>

- [Generic JSON Validator](http://jsonlint.com)
- [Minecraft Wiki: Loot table](http://minecraft.gamepedia.com/Loot_table)
- [Javascript loot table generator](https://jsfiddle.net/MrPingouin/125mx5r5/embedded/result) by [MrPingouin1](http://www.minecraftforum.net/members/MrPingouin1) (for Java edition)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conclusion">Conclusion</a>

If there is information that needs to be added, corrected, or clarified, please let me know (such as creating a new issue). I do not play Bedrock edition, so I can only go so far with this myself.
