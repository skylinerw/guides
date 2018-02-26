# Shared data structures

<a name="table-of-contents"/>

This is a list of various data structures that the game uses for multiple different advancement triggers. These data structures are not advancements on their own; rather, they are bits of information that are copied into an advancement trigger's [conditions](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md). For example, the [`minecraft:slept_in_bed`](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md#slept_in_bed), [`minecraft:location`](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md#location), and [`minecraft:nether_travel`](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md#nether_travel) advancement **triggers** all use the same shared ["location" **data structure**](#generic-location) within its `conditions` object.

Since a lot of triggers share the same data structures, separating them into this list greatly reduces the character length of the guide. Each trigger that makes use of a shared data structure will still have an example usage of that data structure, while this list will provide all possible options of that structure.

1. [Example](#shared-example)
1. [Range](#generic-range)
1. [Location object](#generic-location)
1. [Distance object](#generic-distance)
1. [Status effects object](#generic-effects)
1. [Item object](#generic-item)
1. [Entity object](#generic-entity)
1. [Block object](#generic-block)
1. [Damage object](#generic-damage)
1. [Damage flags object](#generic-damage-flags)
1. [Death object](#generic-death)

## [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="shared-example">Example</a>

On the [`minecraft:entity_hurt_player`](https://github.com/skylinerw/guides/blob/master/java/advancements/triggers.md#entity_hurt_player) trigger page, the first thing listed is what the advancement does when no options are specified. Below that is a list of "**Conditions**", with each condition stating when it's making use of one of the data structures listed here (in this case, it uses the shared [damage data structure](#generic-damage)). Below that is an example of that data structure being used.

![](http://skylinerw.com/images/advancements/structures_1.png)

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
