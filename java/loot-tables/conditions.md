# Conditions

<a name="table-of-contents"/>

The following is a list of all possible conditions that can be used within loot tables.

1. [`random_chance`](#random_chance)
2. [`random_chance_with_looting`](#random_chance_with_looting)
3. [`killed_by_player`](#killed_by_player)
4. [`entity_properties`](#entity_properties)
    1. [`on_fire`](#entity_properties-on_fire)
5. [`entity_scores`](#entity_scores)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="random_chance">`random_chance`</a>

Adds a modifier to the drop chances of the item/pool or chance to execute a function. The `chance` float key will hold a value between 0.0 and 1.0, with 1.0 being 100% chance.

The following has a 30% chance to provide smooth stone. Otherwise, it provides cobblestone.

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
                                    "condition": "minecraft:random_chance",
                                    "chance": 0.3
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

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="random_chance_with_looting">`random_chance_with_looting`</a>

Modifies the chances of success based on the Looting enchantment on the item in the mainhand of the killing entity. Does not need to be a player entity, but must extend "LivingBase" (essentially all mobs, whether hostile or passive, as well as armor stands). The `chance` float key holds the base chance value while the `looting_multiplier` float key modifies the chances based on the Looting level.

The following formula is used to determine the chances.

```
chance + (looting_level * looting_multiplier)
```

The following will smelt the cobblestone at a base 10% chance, increasing by 15% with each level of Looting. For example, with Looting 3, the following smelts cobblestone 55% of the time (`0.1 + (3 * 0.15) = 0.55`).

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
                                    "condition": "minecraft:random_chance_with_looting",
                                    "chance": 0.1,
                                    "looting_multiplier": 0.15
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

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="killed_by_player">`killed_by_player`</a>

Provides an item/pool or executes a function if the mob was specifically killed by a player. The `inverse` boolean key, when set to true, will only do so if the mob was **not** killed by a player.

The following will smelt the cobblestone only if the mob was not killed by a player.

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
                                    "condition": "minecraft:killed_by_player",
                                    "inverse": true
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

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entity_properties">`entity_properties`</a>

Provides an item/pool or executes a function if the target entity defined by the `entity` string key has the properties defined within the `properties` compound.

`entity` is restricted to: ["this"](https://github.com/skylinerw/guides/blob/master/java/loot%20tables.md#construct-this), ["killer"](https://github.com/skylinerw/guides/blob/master/java/loot%20tables.md#construct-damage), and ["killer_player"](https://github.com/skylinerw/guides/blob/master/java/loot%20tables.md#construct-player). Target selectors or exact names do **not** work.

The IDs for properties will have the resource namespace default to "minecraft" (e.g. `minecraft:on_fire`) when not specified. If using mods, the namespace will be whatever the mod has implemented. Note that you cannot create your own properties without modding.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entity_properties-on_fire">`on_fire`</a>

The following will only provide a stick if the mob was killed while it was on fire.

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

The following will only provide cobblestone if the killing player was **not** on fire.

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
                            "entity": "killer_player",
                            "properties": {
                                "minecraft:on_fire": false
                            }
                        }
                    ]
                }
            ]
        }
    ]
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entity_scores">`entity_scores`</a>

Provides an item/pool or executes a function if the target entity defined by the `entity` string key has the scores defined within the `scores` compound.

`entity` is restricted to: ["this"](https://github.com/skylinerw/guides/blob/master/java/loot%20tables.md#construct-this), ["killer"](https://github.com/skylinerw/guides/blob/master/java/loot%20tables.md#construct-damage), and ["killer_player"](https://github.com/skylinerw/guides/blob/master/java/loot%20tables.md#construct-player). Target selectors or exact names do **not** work. Note that because of this, you may need to copy fake player scores to your desired killers/victims.

Within the `scores` compound will either be an integer or another compound, both of which uses the objective name for its own key name.

The following will only provide stone if the killing player had an "ObjA" score of exactly 1, as well as an "ObjB" score between values 1 and 25.

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
                            "entity": "killer_player",
                            "scores": {
                                "ObjA": 1,
                                "ObjB": {
                                    "min": 1,
                                    "max": 25
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
