# Triggers

<a name="table-of-contents"/>

Certain triggers make use of subcriteria, specified in the `conditions` object. The data stored within is dependent on the type of trigger, and not all triggers need or use it. The following is a list of all possible triggers that can be used within advancements, along with any extra applicable data.

1. [`bred_animals`](#bred_animals)
1. [`brewed_potion`](#brewed_potion)
1. [`changed_dimension`](#changed_dimension)
1. [`construct_beacon`](#construct_beacon)
1. [`consume_item`](#consume_item)
1. [`cured_zombie_villager`](#cured_zombie_villager)
1. [`effects_changed`](#effects_changed)
1. [`enchanted_item`](#enchanted_item)
1. [`enter_block`](#enter_block)
1. [`entity_hurt_player`](#entity_hurt_player)
1. [`entity_killed_player`](#entity_killed_player)
1. [`impossible`](#impossible)
1. [`inventory_changed`](#inventory_changed)
1. [`item_durability_changed`](#item_durability_changed)
1. [`levitation`](#levitation)
1. [`location`](#location)
1. [`nether_travel`](#nether_travel)
1. [`placed_block`](#placed_block)
1. [`player_hurt_entity`](#player_hurt_entity)
1. [`player_killed_entity`](#player_killed_entity)
1. [`recipe_unlocked`](#recipe_unlocked)
1. [`slept_in_bed`](#slept_in_bed)
1. [`summoned_entity`](#summoned_entity)
1. [`tame_animal`](#tame_animal)
1. [`tick`](#tick)
1. [`used_ender_eye`](#used_ender_eye)
1. [`used_totem`](#used_totem)
1. [`villager_trade`](#villager_trade)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="bred_animals">`bred_animals`</a>

This triggers when the player successfully breeds two animals. If two players cause a pair of animals to breed, the animal that "births" the baby will trigger the advancement for whoever fed that animal. For example, the following succeeds if the player breeds any two animals together:

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals"
        }
    }
}
```

### Conditions

There are 3 conditions for this trigger: `parent`, `partner`, and `child`.

#### 1 & 2. "parent", "partner"

Both the `parent` and `partner` [entity objects](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity) describe either of the parents. Both entities in the taming process could be the parent or the partner. For example, all of the following triggers checks if **either** parent is a cow.
 
```json
{
    "criteria": {
        "custom_test_name_1": {
            "trigger": "minecraft:bred_animals",
            "conditions": {
                "parent": {
                    "type": "minecraft:cow"
                }
            }
        },
        "custom_test_name_2": {
            "trigger": "minecraft:bred_animals",
            "conditions": {
                "partner": {
                    "type": "minecraft:cow"
                }
            }
        }
    }
}
```

A time that you'd use both `parent` and `partner` is when you want to check to see if the entity types are a specific pairing, whether it be two of the same entity or two different entities. The following will trigger only if the parents are a mix of a cow and a sheep, which **cannot** be fulfilled in vanilla.
 
```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals",
            "conditions": {
                "parent": {
                    "type": "minecraft:cow"
                },
                "partner": {
                    "type": "minecraft:sheep"
                }
            }
        }
    }
}
```

The only animal pairing that can have different parents are horses and donkeys, which create a mule. If you wanted to check if the player bred a horse and a horse together, you **must** specify both the parent and partner as a horse because otherwise it would trigger if the player bred a horse and a donkey.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals",
            "conditions": {
                "parent": {
                    "type": "minecraft:horse"
                },
                "partner": {
                    "type": "minecraft:horse"
                }
            }
        }
    }
}
```

But if you wanted to check if a mule is being created, you'd look for a donkey and a horse (the order does not matter).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals",
            "conditions": {
                "parent": {
                    "type": "minecraft:donkey"
                },
                "partner": {
                    "type": "minecraft:horse"
                }
            }
        }
    }
}
```

#### 3. "child", "partner"

The `child` [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity) will match against the newborn animal. The following will trigger only if the child is a cow.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals",
            "conditions": {
                "child": {
                    "type": "minecraft:cow"
                }
            }
        }
    }
}
```

You can also specify the parents and child. For example, the following triggers if the parents include a horse and a donkey, while the child is a mule (although that makes the check for parents useless as that's the only way to get a mule).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:bred_animals",
            "conditions": {
                "parent": {
                    "type": "minecraft:horse"
                },
                "partner": {
                    "type": "minecraft:donkey"
                },
                "child": {
                    "type": "minecraft:mule"
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="brewed_potion">`brewed_potion`</a>

This triggers when the player removes **any** item from the output slots of a brewing stand. Note that this doesn't mean the player has to have brewed it; any brewing stands that generate with potions inside it (such as in the End Ship) will fulfill this trigger. The player can repeatedly place a potion into a brewing stand and remove it to constantly fulfill this trigger.

For example, the following will trigger if the player removes **any** item from a brewing stand:

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:brewed_potion"
        }
    }
}
```

### Conditions

There is 1 condition for this trigger: `potion`.

#### 1. "potion"

The `potion` string allows you to specify a default brewed potion ID (stored in the `Potion` string tag on the item) that the removed item must contain. The wiki contains a list of those [here](https://minecraft.gamepedia.com/Potion#Item_data). If the item removed does not have a `Potion` string tag, the trigger will assume the ID is "minecraft:empty".

The following only triggers if the player removes an extended Invisibility potion from the brewing stand's output slots.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:brewed_potion",
            "conditions": {
                "potion": "minecraft:long_invisibility"
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="changed_dimension">`changed_dimension`</a>

This triggers whenever the player switches to another dimension.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:changed_dimension"
        }
    }
}
```

### Conditions

There are 2 conditions for this trigger: `to` and `from`.

#### 1. "to"

The `to` string specifies the dimension the player is traveling to, accepting values "overworld", "the_nether", and "the_end". The following triggers if the player travels to the end (regardless of their original dimension).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:changed_dimension",
            "conditions": {
                "to": "the_end"
            }
        }
    }
}
```

#### 2. "from"

The `from` string specifies the dimension the player traveled from, accepting values "overworld", "the_nether", and "the_end". The following triggers if the player travels to the end, though specifically from the nether.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:changed_dimension",
            "conditions": {
                "to": "the_end",
                "from": "the_nether"
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="construct_beacon">`construct_beacon`</a>

This triggers every time the player receives an effect update from a beacon, **not** when physically constructing a beacon pyramid. The beacon does not need to have any effects selected for this update to occur, but the player does need to be in range as usual. The beacon does **not** need to be placed on a valid pyramid structure!

For example, the following will trigger if the player is close enough to a beacon to receive its effect, regardless of an effect is selected or even if there is a valid pyramid beneath the beacon.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:construct_beacon"
        }
    }
}
```

### Conditions

There is 1 condition for this trigger: `level`.

#### 1. "level"

The `level` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) specifies the number of levels the beacon pyramid has, up to 4. For example, the following checks if the beacon pyramid has at least 2 levels when the player receives an effect update.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:construct_beacon",
            "conditions": {
                "level": {"min": 2}
            }
        }
    }
}
```

While the following checks if the player is receiving an update from a beacon that does **not** have a pyramid associated with it.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:construct_beacon",
            "conditions": {
                "level": 0
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="consume_item">`consume_item`</a>

This triggers whenever the player eats food, drinks milk, or drinks a potion/water bottle.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:consume_item"
        }
    }
}
```

### Conditions

There is 1 conditions for this trigger: `item`.

#### 1. "item"

The `item` [item object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-item) specifies item data about the item **before** it was consumed. For example, the following activates if the player had 2 golden apples in the stack and ate 1 of them.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:consume_item",
            "conditions": {
                "item": {
                    "item": "minecraft:golden_apple",
                    "count": 2
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="cured_zombie_villager">`cured_zombie_villager`</a>

This triggers whenever a zombie villager completes its conversion into a villager. The player that had cured it will be the one fulfilling the trigger.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:cured_zombie_villager"
        }
    }
}
```

### Conditions

There are 2 conditions for this trigger: `zombie` and `villager`.

#### 1. "zombie"

The `zombie` [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity) specifies entity data about the zombie that was converted. The `type` string is essentially useless here as it will always be "minecraft:zombie_villager".

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the entity will be the zombie's location. That is, the value will describe the distance between the cured zombie and the player. The following checks if the player cured a zombie but was 50+ blocks away from it when the conversion was completed.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:cured_zombie_villager",
            "conditions": {
                "zombie": {
                    "distance": {
                        "min":50
                    }
                }
            }
        }
    }
}
```

#### 2. "villager"

The `villager` [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity) specifies entity data about the villager that was created as a result of converting. The `type` string is essentially useless here as it will always be "minecraft:villager".

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the entity will be the villager's location. That is, the value will describe the distance between the new villager and the player. The following checks if the player cured a zombie but was 50+ blocks away from it when the conversion was completed.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:cured_zombie_villager",
            "conditions": {
                "villager": {
                    "distance": {
                        "min":50
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="effects_changed">`effects_changed`</a>

This triggers whenever the player receives or loses an effect.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:effects_changed"
        }
    }
}
```

There is 1 condition for this trigger: `effects`.

#### 1. "effects"

A [status effects](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-effects) object that checks the player's whole list of effects whenever any effect is applied or lost. However, keep in mind that if the player lost an effect to trigger this advancement, it cannot be detected. The following checks if the player has Levitation.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:effects_changed",
            "conditions": {
                "effects": {
                    "minecraft:levitation": {
                        
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="enchanted_item">`enchanted_item`</a>

This triggers whenever the player enchants an item in an enchanting table. The player does not have to remove the item, as it's triggered when selecting which enchantment to apply. The following triggers upon enchanting.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:enchanted_item"
        }
    }
}
```

### Conditions

There are 2 conditions for this trigger: `item` and `levels`.

#### 1. "item"

The `item` [item object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-item) matches the enchanted item against the provided data. Note that this check occurs **after** enchanting, which means that you can make use of the `enchantments` list to only fulfill the trigger if a specific enchantment was received. The following will trigger if the item that was enchanted is a diamond pickaxe, and received any level of Fortune.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:enchanted_item",
            "conditions": {
                "item": {
                    "item": "minecraft:diamond_pickaxe",
                    "enchantments": [
                        {
                            "enchantment": "minecraft:fortune"
                        }
                    ]
                }
            }
        }
    }
}
```

#### 2. "levels"

The `levels` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) specifies the number of levels spent to receive the enchantment. For example, with 15 bookshelves, selecting the third option requires 30 levels and **costs** 3 levels. This condition checks that cost of 3 levels. This does mean that you can't ensure the player had to have 30 levels from the advancement alone, but could use in-game command blocks to help detect that.

The following will trigger if the player spends 3 levels on an enchantment.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:enchanted_item",
            "conditions": {
                "levels": 3
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="enter_block">`enter_block`</a>

This triggers whenever the player's hitbox intersects with a block, **including air**, as well as the block at the location an enderpearl had landed at. As such, the following triggers constantly because the player is always in a block.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:enter_block"
        }
    }
}
```

### Conditions

This trigger uses only the [block object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-block) to check various data.

#### 1. shared block object

The following checks if the player is standing in a fern.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:enter_block",
            "conditions": {
                "block": "minecraft:tallgrass",
                "state": {
                    "type": "fern"
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entity_hurt_player">`entity_hurt_player`</a>

This triggers whenever the player takes any amount of damage (including 0), even when blocking.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player"
        }
    }
}
```

### Conditions

There is 1 condition for this trigger: `damage`.

#### 1. "damage"

A [damage object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-damage) that describes the damage the player has taken, complete with entity source that dealt the damage. For example, the following checks if the player had taken 10+ health damage from an explosion caused by a creeper.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player",
            "conditions": {
                "damage": {
                    "taken": {
                        "min": 10.0
                    },
                    "type": {
                        "is_explosion": true
                    },
                    "source_entity": {
                        "type": "minecraft:creeper"
                    }
                }
            }
        }
    }
}
```

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the source entity will be the mob's location. That is, the value will describe the distance between the entity causing the damage and the player taking the damage. The following checks if the player had taken damage from a skeleton while being within 3 blocks of that skeleton.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_hurt_player",
            "conditions": {
                "damage": {
                    "source_entity": {
                        "type": "minecraft:skeleton",
                        "distance": {
                            "max": 3
                        }
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="entity_killed_player">`entity_killed_player`</a>

This triggers when any mob (**not** other players) kills the player.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_killed_player"
        }
    }
}
```

### Conditions

This trigger uses only the [death object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-death) to check various data.

#### 1. "entity"

Matches the mob that killed the player against the specified [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity). The following triggers if the player was killed by a creeper.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:entity_killed_player",
            "conditions": {
                "entity": {
                    "type": "minecraft:creeper"
                }
            }
        }
    }
}
```

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the entity will be the mob's location. That is, the value will describe the distance between the player and the mob that killed them. The following checks if the player was killed by a skeleton while being within 3 blocks of it.

```json
{
    "criteria": {
        "noob": {
            "trigger": "minecraft:entity_killed_player",
            "conditions": {
                "entity": {
                    "type": "minecraft:skeleton",
                    "distance": {
                        "max": 3
                    }
                }
            }
        }
    }
}
```

#### 2. "killing_blow"

A [damage flags object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-damage-flags) checking various flags for the damage the player had dealt for the killing blow. The following triggers if the player is killed by a skeleton that didn't deal projectile damage.

```json
{
    "criteria": {
        "skeletal_swordsman": {
            "trigger": "minecraft:entity_killed_player",
            "conditions": {
                "entity": {
                    "type": "minecraft:skeleton"
                },
                "killing_blow": {
                    "is_projectile": false
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="impossible">`impossible`</a>

This does **not** trigger naturally. The only way to trigger this is in vanilla to use the [`/advancement`](https://github.com/skylinerw/guides/blob/master/java/advancements.md#command-advancement) command. This greatly supplements command mechanisms as it allows more complex requirements that can be met with in-game commands.

For example, given the following advancement, assuming resource location "custom:missions":

```json
{
    "criteria": {
        "mission1": {
            "trigger": "minecraft:impossible"
        },
        "mission2": {
            "trigger": "minecraft:impossible"
        }
    }
}
```

The following commands will individually trigger each specific criterion, completing the advancement, but only for players that have the "winner" tag:

```
/advancement grant @a[tag=winner] custom:missions mission1
/advancement grant @a[tag=winner] custom:missions mission2
```

### Conditions

There are 0 conditions for this trigger.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="inventory_changed">`inventory_changed`</a>

This triggers whenever the player's inventory is updated, such as from removing an item or adding an item. Note that spreading items through the inventory by holding left or right-click will count as multiple actions, though is only relevant if the advancement is revoked by a [function reward](https://github.com/skylinerw/guides/blob/master/java/advancements.md#rewards) during that tick. The following will trigger from any addition or removal of items from the player's inventory:

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:inventory_changed"
        }
    }
}
```

### Conditions

There are 2 conditions for this trigger: `slots` and `items`.

#### 1. "slots"

The `slots` object contains generic information about all slots in the inventory. Within it are three possible [ranges](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) to specify: `occupied`, `full`, and `empty`. The armor and offhand slots are included in these checks.

The `occupied` range indicates the number of slots that have an item in it. The `full` range indicates the number of slots that have the highest number of items possible for that slot (such as 1 diamond pickaxe or 64 stone blocks). The `empty` range indicates the number of empty slots.

For example, the following will trigger when the player has exactly 10 empty slots in their inventory at the time that their inventory gets updated.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:inventory_changed",
            "conditions": {
                "slots": {
                    "empty": 10
                }
            }
        }
    }
}
```

#### 2. "items"

The `items` [item object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-item) list matches the player's inventory against each item provided in the list. The inventory **must** match all items specified. The following checks if the player has both stone and dirt in their inventory at the time their inventory gets updated.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:inventory_changed",
            "conditions": {
                "items": [
                    {
                        "item": "minecraft:stone",
                        "data": 0
                    },
                    {
                        "item": "minecraft:dirt"
                    }
                ]
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="item_durability_changed">`item_durability_changed`</a>

This triggers whenever an item in the player's inventory has resulted in a loss of durability. The following will trigger whenever any item in the player's inventory takes damage: 

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:item_durability_changed"
        }
    }
}
```

### Conditions

There are 3 conditions for this trigger: `item`, `durability`, and `delta`.

#### 1. "item"

The `item` [item object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-item) matches the damaged item with the data provided. This specifically checks the item **before** it was damaged, allowing you to check its durability and other data prior to durability loss. The following checks if the player used a diamond sword that had 1540 or more durability remaining before being damaged.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:item_durability_changed",
            "conditions": {
                "item": {
                    "item": "minecraft:diamond_sword",
                    "durability": {
                        "min": 1540
                    }
                }
            }
        }
    }
}
```

#### 2. "durability"

The `durability` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) checks the item's remaining durability **after** the item was damaged. The following checks if a shield had at most 10 durability remaining after being damaged.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:item_durability_changed",
            "conditions": {
                "durability": {
                    "max": 10
                }
            }
        }
    }
}
```

#### 3. "delta"

The `delta` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) checks the change in durability of the item. For example, the following checks if the item had a change of -2 durability or more (took 2+ points of damage).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:item_durability_changed",
            "conditions": {
                "delta": {
                    "max": -2
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="levitation">`levitation`</a>

This triggers whenever the player is under the Levitation potion effect.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:levitation"
        }
    }
}
```

### Conditions

There are 2 conditions for this trigger: `duration` and `distance`.

#### 1. "duration"

The `duration` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) checks how the number of ticks the player has been levitating (not how long the effect itself was set to last). Note that this internal timer will not reset until the Levitation effect is removed, thus revoking the advancement after it was achieved will simply cause it to be achieved immediately after if the player is still under the Levitation effect. The following triggers when the player has been under the Levitation effect for 40 or more ticks.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:levitation",
            "conditions": {
                "duration": {
                    "min": 40
                }
            }
        }
    }
}
```

#### 2. "distance"

The `distance` [distance object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-distance) checks how the number of blocks away the player has traveled from the moment they received the effect. The following checks if the player has is still within 10 blocks (regardless of direction) of where they received the effect.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:levitation",
            "conditions": {
                "distance": {
                    "absolute": {
                        "max": 10
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="location">`location`</a>

This triggers every second (every 20 ticks) at all times, essentially requiring the use of conditions despite them being optional for this trigger. However, this activation can be useful if needing pre-finished advancements. The following will always activate every second.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:location"
        }
    }
}
```

### Conditions

This trigger uses only the [location object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-location) to check various data. The origin of the location is the player's coordinates.

#### 1. "position"

The following checks if the player is between Y0 and Y30.

```json
{
    "criteria": {
        "below_sea_level": {
            "trigger": "minecraft:location",
            "conditions": {
                "position": {
                    "y": {
                        "min": 0.0,
                        "max": 30.0
                    }
                }
            }
        }
    }
}
```

#### 2. "biome"

The following will trigger once the player eventually visits both the "desert" and "plains" biomes.

```json
{
    "criteria": {
        "visit_desert": {
            "trigger": "minecraft:location",
            "conditions": {
                "biome": "minecraft:desert"
            }
        },
        "visit_plains": {
            "trigger": "minecraft:location",
            "conditions": {
                "biome": "minecraft:plains"
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="nether_travel">`nether_travel`</a>

This triggers every second (every 20 ticks) at all times, essentially requiring the use of conditions despite them being optional for this trigger. However, this activation can be useful if needing pre-finished advancements. The following will always activate every second.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:nether_travel"
        }
    }
}
```

### Conditions

There are 3 conditions for this trigger: `entered`, `exited`, and `distance`.


This trigger uses only the [location object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-location) to check various data. The origin of the location is the player's coordinates.

#### 1. "entered"

A [location object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-location) checking the location of the nether portal that the player entered the nether through. The following checks if that nether portal was in a desert biome.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:nether_travel",
            "conditions": {
                "entered": {
                    "biome": "minecraft:desert"
                }
            }
        }
    }
}
```

#### 2. "exited"

A [location object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-location) checking the location of the nether portal that the player arrived to in the overworld after leaving the nether through a portal. The following checks if that nether portal was in the positive X and Z coordinates.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:nether_travel",
            "conditions": {
                "exited": {
                    "position": {
                        "x": {
                            "min": 0
                        },
                        "z": {
                            "min": 0
                        }
                    }
                }
            }
        }
    }
}
```

#### 3. "distance"

A [distance object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-distance) checking the distance between the two nether portals positioned in the overworld. The following checks if they are horizontally 500 blocks apart.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:nether_travel",
            "conditions": {
                "distance": {
                    "horizontal": {
                        "min": 500
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="placed_block">`placed_block`</a>

This triggers whenever the player places a block from their inventory.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:placed_block"
        }
    }
}
```

### Conditions

This trigger makes use of the [shared block object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-block), and has 2 extra conditions for this trigger: `location` and `item`.

#### 1. shared block object

The block to be checked. The following checks if the player placed down smooth andesite.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:placed_block",
            "conditions": {
                "block": "minecraft:stone",
                "state": {
                    "variant": "smooth_andesite"
                }
            }
        }
    }
}
```

#### 2. "item"

An [item object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-item) that checks information concerning the corresponding item that the player used to place the block, **before** the item was consumed. The following checks if the player placed an unpowered repeater block (where the corresponding item is "minecraft:repeater") that was the last item in the stack at the time of placing.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:placed_block",
            "conditions": {
                "block": "minecraft:unpowered_repeater",
                "item": {
                    "item": "minecraft:repeater",
                    "count": 1
                }
            }
        }
    }
}
```

#### 3. "location"

A [location object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-location) that checks various location data about where the block was placed. For example, the following checks if glass block was placed anywhere at Y128 or higher.

```json
{
    "criteria": {
        "super_skylight": {
            "trigger": "minecraft:placed_block",
            "conditions": {
                "block": "minecraft:glass_block",
                "location": {
                    "position": {
                        "y": {
                            "min": 128
                        }
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="player_hurt_entity">`player_hurt_entity`</a>

This triggers whenever the player deals any amount of damage to an entity, including 0 (e.g. snowballs).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_hurt_entity"
        }
    }
}
```

### Conditions

There are 2 conditions for this trigger: `damage` and `entity`.

#### 1. "damage"

A [damage object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-damage) that describes the damage the entity had taken by the player, complete with entity source to check information about the player. The `type` string in that case is essentially useless because it will always be "minecraft:player". The following checks if the player dealt at least 10.0 raw projectile damage before damage reduction from the entity's gear, allowing for only up to 10% damage reduction.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_hurt_entity",
            "conditions": {
                "damage": {
                    "dealt": {
                        "min": 10.0
                    },
                    "taken": {
                        "min": 9.0
                    },
                    "type": {
                        "is_projectile": true
                    }
                }
            }
        }
    }
}
```

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the source entity will be the player's location. That is, the value will describe the distance between the player and.. the player. This renders the option essentially useless. For example, the following checks if the player was within 3 blocks of themselves after damaging an entity, which is always true.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_hurt_entity",
            "conditions": {
                "damage": {
                    "source_entity": {
                        "distance": {
                            "max": 3
                        }
                    }
                }
            }
        }
    }
}
```

#### 2. "entity"

An [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity) that checks information concerning the entity that the player had damaged. The following checks if the player dealt projectile damage to a zombie.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_hurt_entity",
            "conditions": {
                "damage": {
                    "type": {
                        "is_projectile": true
                    }
                },
                "entity": {
                    "type": "minecraft:zombie"
                }
            }
        }
    }
}
```

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for this entity will be the entity's location. That is, the value will describe the distance between the entity taking the damage and the player dealing the damage. The following correctly checks if the player was within 3 blocks of the entity they damaged.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_hurt_entity",
            "conditions": {
                "entity": {
                    "distance": {
                        "max": 3
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="player_killed_entity">`player_killed_entity`</a>

This triggers when the player kills any entity extending the "Living" class (only mobs, **not** other players).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_killed_entity"
        }
    }
}
```

### Conditions

This trigger uses only the [death object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-death) to check various data.

#### 1. "entity"

Matches the entity killed against the specified [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity). The following triggers if the player kills a cow.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_killed_entity",
            "conditions": {
                "entity": {
                    "type": "minecraft:cow"
                }
            }
        }
    }
}
```

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the entity will be the mob's location. That is, the value will describe the distance between the entity that died and the player that killed it. The following checks if the player had killed a skeleton with a projectile while being 50+ blocks away from it.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_killed_entity",
            "conditions": {
                "entity": {
                    "type": "minecraft:skeleton",
                    "distance": {
                        "min": 50
                    }
                },
                "killing_blow": {
                    "is_projectile": true
                }
            }
        }
    }
}
```

#### 2. "killing_blow"

A [damage flags object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-damage-flags) checking various flags for the damage the player had dealt for the killing blow. The following triggers if the player kills a creeper **without** projectile damage.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:player_killed_entity",
            "conditions": {
                "entity": {
                    "type": "minecraft:creeper"
                },
                "killing_blow": {
                    "is_projectile": false
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="recipe_unlocked">`recipe_unlocked`</a>

This triggers when the player unlocks a recipe. There is 1 **required** condition for this trigger: `recipe`, which specifies the resource location to the desired recipe that must be unlocked. Note that this check only occurs at the time of receiving the recipe; if the player unlocked it before the advancement exists, the advancement will not take that into consideration. As the recipe is only detected at the moment of unlocking it, you must revoke the recipe from the player using the `/recipe` command before it can be detected again.

The following will trigger when the player unlocks the "minecraft:redstone" recipe, in whatever way they unlock it (including as a reward for another advancement).

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:recipe_unlocked",
            "conditions": {
                "recipe": "minecraft:redstone"
            }
        }
    }
}
```

### Conditions

There are 0 optional conditions for this trigger.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="slept_in_bed">`slept_in_bed`</a>

This triggers when the player successfully sleeps in a bed. If the bed explodes, such as from trying to sleep in the nether, this will not trigger.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:slept_in_bed"
        }
    }
}
```

### Conditions

This trigger uses only the [location object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-location) to check various data. The origin of the location is the player's coordinates **after** getting in the bed, which would be just above the bed's head.

#### 1. "position"

The following checks if the player is below Y10 once they get in a bed.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:slept_in_bed",
            "conditions": {
                "position": { 
                    "y": {
                        "max": 10
                    }
                }
            }
        }
    }
}
```

#### 2. "biome"

The following will trigger when the player sleeps in a bed while their position indicates the biome their new position is in, being where the head of the bed is. While this was seemingly implemented as a way to detect the player sleeping in the nether, the bed exploding will not cause the trigger to be fulfilled. The following checks if the player slept in a desert biome.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:slept_in_bed",
            "conditions": {
                "biome": "minecraft:desert"
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="summoned_entity">`summoned_entity`</a>

This triggers when the player creates an entity in specific manners. Valid methods are: constructing a wither, constructing a snow golem, constructing an iron golem, and respawning the ender dragon. In the case of the ender dragon, all players able to view the boss bar will fulfill the trigger. The following triggers when the player summons any of the valid entities.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:summoned_entity"
        }
    }
}
```

### Conditions

There is 1 condition for this trigger: `entity`.

#### 1. "entity"

Matches the entity summoned against the specified [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity). The following triggers if the player resummons the ender dragon.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:summoned_entity",
            "conditions": {
                "entity": {
                    "type": "minecraft:ender_dragon"
                }
            }
        }
    }
}
```

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the entity will be the summoned mob's location. That is, the value will describe the distance between the entity that was summoned and the player that summoned it, or between the ender dragon and all elligible players. The following checks if the player is within 10 blocks of the ender dragon when the summoning ritual is complete, which would be very high in the air.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:summoned_entity",
            "conditions": {
                "entity": {
                    "type": "minecraft:ender_dragon",
                    "distance": {
                        "max": 10
                    }
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tame_animal">`tame_animal`</a>

This triggers when the player tames any animal.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:tame_animal"
        }
    }
}
```

### Conditions

There is 1 condition for this trigger: `entity`.

#### 1. "entity"

The `entity` [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity) specifies the entity that the player tamed. The following checks if the player tamed a wolf.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:tame_animal",
            "conditions": {
                "entity": {
                    "type": "minecraft:wolf"
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="tick">`tick`</a>

This simply triggers every tick. This can be used to help simulate command block systems or automatically unlock advancements. The following activates every tick, running a function as a reward that revokes the advancement so that it may activate in the next tick.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:tick"
        }
    },
    "rewards": {
        "function": "path:to/function"
    }
}
```

### Conditions

There are 0 conditions for this trigger.

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="used_ender_eye">`used_ender_eye`</a>

This triggers when the player uses an Eye of Ender.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:used_ender_eye"
        }
    }
}
```

### Conditions

There is 1 condition for this trigger: `distance`.

#### 1. "distance"

The `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) specifies the number of blocks away the player (not the Eye of Ender) is to the nearest stronghold's center. It only takes into consideration the X and Z coordinate, ignoring the Y coordinate.

The formula for determining the number of blocks is:

```
sqrt((player.X - stronghold.X)^2 + (player.Z - stronghold.Z)^2)
```

For example, if the player was at (40, 64, 10) and the stronghold was at (120, 25, 400), the resulting distance needed to encompass the player is at least 398:

```
sqrt((40 - 120) + (10 - 400)) = 398
```

And the following will trigger if the player is standing at (40, 64, 10) with the stronghold at (120, 25, 400). If the player moves one block further away, they will no longer be detected.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:used_ender_eye",
            "conditions": {
                "distance": {
                    "max": 398
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="used_totem">`used_totem`</a>

This triggers when the player uses a Totem of Undying.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:used_totem"
        }
    }
}
```

### Conditions

There is 1 condition for this trigger: `item`.

#### 1. "item"

The `item` [item object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-item) specifies item data about the item **before** it was consumed. For example, the following activates if the player had 5 totems of undying in the stack before one was consumed to save them.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:used_totem",
            "conditions": {
                "item": {
                    "count": 5
                }
            }
        }
    }
}
```

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="villager_trade">`villager_trade`</a>

This triggers whenever the player completes a trade with a villager. Note that shift-clicking to complete a trade multiple times will only trigger this for the first trade that tick.

```json
{
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:villager_trade"
        }
    }
}
```

### Conditions

There are 2 conditions for this trigger: `villager` and `item`.

#### 1. "villager"

The `villager` [entity object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-entity) describes information about the villager that was traded with. In this case, the `type` string is useless as it will always be "minecraft:villager".

The origin for the `distance` [range](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-range) for the entity will be the villager's location. That is, the value will describe the distance between the villager being traded with and the player that traded with it. The following checks if the player is within 1 block of the villager when completing a trade.

```json
{
    "criteria": {
        "shady_deal": {
            "trigger": "minecraft:villager_trade",
            "conditions": {
                "villager": {
                    "distance": {
                        "max": 1
                    }
                }
            }
        }
    }
}
```

#### 2. "item"

The `item` [item object](https://github.com/skylinerw/guides/blob/master/java/advancements.md#generic-item) describes information about the item that was **purchased**. In terms of the count, keep in mind that this only accounts for one trade. If a villager was selling 2 sugar per emerald, shift-clicking to receive 10 sugar would still only count as 2 sugar as far as the advancement goes, as the first completed trade of the bunch was for 2 sugar. The following checks if the purchased item was sugar.

```json
{
    "criteria": {
        "shady_deal": {
            "trigger": "minecraft:villager_trade",
            "conditions": {
                "item": {
                    "item": "minecraft:sugar"
                }
            }
        }
    }
}
```
