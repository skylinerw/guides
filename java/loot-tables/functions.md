# Functions

<a name="table-of-contents"/>

The following is a list of all possible functions that can be used within loot tables, along with any extra applicable data.

1. [`set_count`](#set_count)
2. [`set_data`](#set_data)
3. [`set_damage`](#set_damage)
4. [`set_nbt`](#set_nbt)
5. [`set_attributes`](#set_attributes)
6. [`enchant_randomly`](#enchant_randomly)
7. [`enchant_with_levels`](#enchant_with_levels)
8. [`looting_enchant`](#looting_enchant)
9. [`furnace_smelt`](#furnace_smelt)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="set_count">`set_count`</a>

Sets the `Count` NBT value of the item either to an exact value or randomly between a minimum and maximum value. The range is defined within the `count` integer or compound. If this function is not used, only one of that item will be dropped.

The following provides exactly 4 stone.

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
                            "function": "minecraft:set_count",
                            "count": 4
                        }
                    ]
                }
            ]
        }
    ]
}
```

The following provides between 3 and 5 stone.

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
                            "function": "minecraft:set_count",
                            "count": {
                                "min": 3,
                                "max": 5
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="set_data">`set_data`</a>

Sets the `Damage` NBT value of the item to the specified value or randomly between a minimum and maximum value. The exact value is defined with the `data` integer key, while the range is defined within the `data` compound.

Cannot be used with items that have durability.

The following provides a mob head with a Damage value of 3 (resulting in a player head).

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:skull",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:set_data",
                            "data": 3
                        }
                    ]
                }
            ]
        }
    ]
}
```

The following provides a dye with a Damage value randomly chosen between 1 and 4.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:dye",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:set_data",
                            "data": {
                                "min": 1,
                                "max": 4
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="set_damage">`set_damage`</a>

Sets the percentage of durability remaining for items that have durability. 1.0 is 100% of durability remaining (undamaged) while 0.0 is no durability remaining. Does not function for items that have no durability. The `damage` float/compound keys will define the value.

The following will either provide either an item at half durability or an item between 75% to 100% durability remaining.

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
                            "function": "minecraft:set_damage",
                            "damage": 0.5
                        }
                    ]
                },
                {
                    "type": "item",
                    "name": "minecraft:diamond_sword",
                    "weight": 1,
                    "functions": [
                        {
                            "function": "minecraft:set_damage",
                            "damage": {
                                "min": 0.75,
                                "max": 1
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="set_nbt">`set_nbt`</a>

Sets the `tag` NBT value of the item. The input is raw NBT data, just like the `show_item` action for hoverEvents in the JSON text component. The value is defined within the `tag` string key. Be sure to escape nested double quotes!

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
                            "function": "minecraft:set_nbt",
                            "tag": "{display:{Name:\"Test\"}}"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="set_attributes">`set_attributes`</a>

Applies attribute modifiers to the item via the `modifiers` list.

Each modifier will require:

1. An `attribute` string that states the attribute ("generic.maxHealth", "generic.armor", etc).
2. A `name` string that states the custom name of the attribute. Can be anything.
3. An `amount` float or compound, stating how much to modify the attribute. If compound, `min` and `max` floats define the range.
4. An `operation` string, stating which operation to use. The accepted inputs are "addition", "multiply_base", and "multiply_total".
5. A `slot` string or list, stating which **one** of the specified slots the item can be used in. Note that if using it as a list, it will randomly selecting **one** of the specified slots. Accepted slots are: "mainhand", "offhand", "feet", "legs", "chest", "head".

Optional input:

1. An `id` string tag that specifies the UUID of the modifier.

The following will provide a diamond sword that will increase the "generic.maxHealth" attribute, choosing a value randomly between 3.5 and 6.0, using the "addition" operation. The slot it is applied to will either be "mainhand" or "offhand".

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
                            "function": "minecraft:set_attributes",
                            "modifiers": [
                                {
                                    "attribute": "generic.maxHealth",
                                    "name": "More Health",
                                    "amount": {
                                        "min": 3.5,
                                        "max": 6
                                    },
                                    "operation": "addition",
                                    "slot": [
                                        "mainhand",
                                        "offhand"
                                    ]
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

The following will apply a "generic.attackDamage" modifier of exactly 1.0 in specifically the "chest" slot. The UUID is `00000000-0000-0001-0000-000000000001`.

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
                            "function": "minecraft:set_attributes",
                            "modifiers": [
                                {
                                    "attribute": "generic.attackDamage",
                                    "name": "More Damage",
                                    "amount": 1,
                                    "operation": "addition",
                                    "slot": "chest",
                                    "id": "00000000-0000-0001-0000-000000000001"
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

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="enchant_randomly">`enchant_randomly`</a>

Randomly applies a single enchantment from a list of enchantment IDs, specified with the optional `enchantments` list. If `enchantments` is not specified, the item will be enchanted randomly with a **compatible** enchantment for that item.

The following will apply a random enchantment and level of either Looting, Depth Strider, or Sharpness, along with a secondary enchantment that is compatible with the item.

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
                            "enchantments": [
                                "minecraft:looting",
                                "minecraft:depth_strider",
                                "minecraft:sharpness"
                            ]
                        },
                        {
                            "function": "minecraft:enchant_randomly"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="enchant_with_levels">`enchant_with_levels`</a>

Applies an enchantment as if it were enchanted through an enchanting table, using a minimum and maximum level defined through the `levels` integer key or compound. The range of levels are the experience levels required for the enchantment, **not** the level of the enchantment applied.

The `treasure` boolean key will allow treasure-only enchantments to be used (such as Mending or Frost Walker). If not defined, it will default to false.

The following provides a diamond sword enchanted at exactly level 30 while allowing the use of treasure-only enchantments.

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
                            "function": "minecraft:enchant_with_levels",
                            "levels": 30,
                            "treasure": true
                        }
                    ]
                }
            ]
        }
    ]
}
```

The following provides a diamond sword with a random enchantment obtained between levels 25 and 30, disregarding treasure.

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
                            "function": "minecraft:enchant_with_levels",
                            "levels": {
                                "min": 25,
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

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="looting_enchant">`looting_enchant`</a>

The number of extra items to drop if the entity has the Looting enchantment on the item in their mainhand. Does not need to be a player entity, but must extend "LivingBase" (essentially all mobs, whether hostile or passive, as well as armor stands). Uses the same `count` integer/compound as `set_count`. The `limit` tag ensures that the resulting number of items cannot exceed that value.

The following provides an extra 30 stone for each level of Looting, but the player can receive no more than 80.

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
                            "count": 30,
                            "limit": 80
                        }
                    ]
                }
            ]
        }
    ]
}
```

The following provides between 10 and 20 extra stone.

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
                            "count": {
                                "min": 10,
                                "max": 20
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="furnace_smelt">`furnace_smelt`</a>

If the item to drop has a smelted crafting recipe, that resulting item will be supplied instead. The following provides charcoal instead of a log.

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "minecraft:log",
                    "weight": 1,
                    "functions": [
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
