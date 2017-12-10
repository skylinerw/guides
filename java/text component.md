# Text Components

*Last updated: 1.12*

***

## Table of contents

#### Generic info

<a name="table-of-contents"/>

1. [Intro](#intro)
2. [Errors](#errors)
3. [JSON Structure](#structure)
4. [Unicode & escape sequences](#unicode)

#### In-game features

5. [Command: `/tellraw`](#feature-tellraw)
6. [Command: `/title`](#feature-title)
7. [Books](#feature-books)
8. [Signs](#feature-signs)
9. [Advancements](#feature-advancements)

#### Text component

10. [Text input](#text)
    1. [`text`](#text-text)
    2. [`translate`](#text-translate)
        1. [Conversion flags & `with`](#text-translate-flags)
        2. [`with` & text components](#text-translate-with)
    3. [`score`](#text-score)
    4. [`selector`](#text-selector)
    5. [`keybind`](#text-keybind)
11. [Text styling](#styling)
    1. [`color`](#styling-color)
    2. [`bold`](#styling-bold)
    3. [`italic`](#styling-italic)
    4. [`underlined`](#styling-underlined)
    5. [`strikethrough`](#styling-strikethrough)
    6. [`obfuscated`](#styling-obfuscated)
12. [Event listeners](#listeners)
    1. [`insertion`](#listeners-insertion)
    2. [`clickEvent`](#listeners-clickevent)
        1. [List of clickEvents](#listeners-clickevents)
    3. [`hoverEvent`](#listeners-hoverevent)
        1. [List of hoverEvents](#listeners-hoverevents)
13. [Adding text](#adding-text)
    1. [`extra`](#adding-text-extra)
    2. [Array structure](#adding-text-array)


#### Additional usage info

14. [Inheritance](#inheritance)
15. [Senders & origins](#origins)
    1. [Initial parsing](#origins-parsing)
    2. [`run_command` clickEvents](#origins-clickevents)
16. [Command: `/trigger`](#command-trigger)

#### Conclusion

17. [Q&A](#qa)
18. [External links](#external-links)
19. [Conclusion](#conclusion)

## Generic info

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="intro">Intro</a>

The "Text Component" is Minecraft's text handling system, in which the text to parse is stored as [JSON](http://json.org). It features basic text styling such as color and formatting (bold, italic, underline), parsing of anonymous data such as target selectors or playerscores, use of translations, as well as advanced options through clickEvents and hoverEvents to, for example, run a command when clicked or display more data when the mouse hovers over text.

This thread will cover how to use the text component with various in-game features, but not all component options are available for all features. Below is a key that will indicate what features a component option if available for.

![](http://www.skylinerw.com/images/json/icons/tellraw.png) = **/tellraw**

![](http://www.skylinerw.com/images/json/icons/title.png) = **/title**

![](http://www.skylinerw.com/images/json/icons/book.png) = **Books**

![](http://www.skylinerw.com/images/json/icons/sign.png) = **Signs**

![](http://www.skylinerw.com/images/json/icons/advancement.png) = **Advancements**

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="errors">Errors</a>

You can validate your JSON using a generic validator such as [JSONLint](http://jsonlint.com).

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="structure">JSON structure</a>

The following is a list of all possible keys for the text component. The wiki will have an up-to-date version [here](http://minecraft.gamepedia.com/Commands#Raw_JSON_text) if this is outdated.

```json
{
    "text": "",
    "translate": "",
    "with": [],
    "score": {
        "name": "",
        "objective": "",
        "value": ""
    },
    "selector": "",
    "keybind": "",
    "color": "",
    "bold": false,
    "italic": false,
    "underlined": false,
    "strikethrough": false,
    "obfuscated": false,
    "insertion": "",
    "clickEvent": {
        "action": "",
        "value": ""
    },
    "hoverEvent": {
        "action": "",
        "value": ""
    },
    "extra": []
}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="unicode">Unicode & escape sequences</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

The text component supports the use of [Java's escape sequences](http://web.cerritos.edu/jwilson/SitePages/java_language_resources/Java_Escape_Sequences.html), which includes unicode support. Not all escape sequences are supported, and not all unicode characters may be visible.

**\u0000 to \uFFFF** ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

Specifies a unicode character, replacing `0000` with the hex value. See [here](http://unicode.org/charts) for unicode characters. For example, the following produces the pilcrow/paragraph sign, being unicode character `00B6`:

```
/tellraw @a {"text":"\u00B6"}
```
![](http://www.skylinerw.com/images/textcomponent/unicode1.png)

**\n** ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

"Newline" character, pushing text to the next line. While with signs it can be used, the text pushed to the next line will not be visible.

```
/tellraw @a ["Line 1\nLine 2"]
```
![](http://www.skylinerw.com/images/textcomponent/unicode2.png)

**\" and \' and \\** ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

Allows the usage of quotation marks nested within quotation marks, such as NBT data for various click/hover events, as well as backslashes as literal characters rather than as an escape sequence.

```
/tellraw @a ["This text is \"quoted\". Backslash: \\"]
```
![](http://www.skylinerw.com/images/textcomponent/unicode3.png)

Note that for nested quotes, deeper-nested quotation marks will need to be escaped further. The formula to obtain the number of backslashes needed is `(2 * [current backslashes]) + 1`. For example, if a quotation mark is needed as a literal value, but is already inside a set of quotes that has 1 backslash, 3 backslashes are needed in order to use the quotation marks:

```
/summon Creeper ~ ~1 ~ {CustomName:"the \"quoted\" text"}

/tellraw @a {"text":"Click","clickEvent":{"action":"run_command","value":"/testfor @e[type=Creeper] {CustomName:\"the \\\"quoted\\\" text\"}"}}
```

The next depth would be 7, then 15, and so on.

**\t and \b and \f and \r**

None of these escape characters can be used with the text component.

## In-game features

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="feature-tellraw">Command: `/tellraw`</a>

The `/tellraw` command adds a message to the chat for specific players. It has access to most features of the text component.

```
/tellraw <player> <text component>

/tellraw @a {"text":"Hello","color":"red","italic":true}
```

**Unavailable features:**

1. `open_file` [click event](#listeners-clickevent).
2. `change_page` [click event](#listeners-clickevent).

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="feature-title">Command: `/title`</a>

The `/title` command adds a message at the center of the screen for specific players. It has access to most basic features of the text component, but lacks support for event listeners. The wiki lists command syntax and general usage info for `/title` [here](http://minecraft.gamepedia.com/Commands#title).

```
/title <player> <title|subtitle> <text component>

/title @a subtitle {"text":"The smaller subtitle","color":"red","italic":true}
/title @a title {"text":"The larger title","color":"red","italic":true}
```

**Unavailable features:**

1. **All** [event listeners](#listeners).

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="feature-books">Books</a>

Currently only the `pages` list tag supports strings that contains a JSON object. The `title` and `author` string tags do not. In order for a book to be considered "valid", the `title` tag must be at maximum 32 characters long. Any higher and the book will only display `* Invalid book tag*`.

Because the NBT data must be string data, quotation marks must be used as other characters would invalidate the JSON object. This also means that any nested quotation marks must be [escaped](#unicode).

While the JSON format for pages can be lenient (i.e. without required quotation marks around all key names and strings), it is **not** recommended. All other usage of the text component has switched to strict, and books will do so in the future.

Note that a bug currently causes inconsistencies with text styling between books and other features using the text component (see [MC-62866](https://bugs.mojang.com/browse/MC-62866)).

```
/give @p minecraft:written_book 1 0 {title:"",author:"",pages:["{\"text\":\"Page 1\",\"italic\":true}"]}
```

**Unavailable features:**

1. `insertion` [event listener](#listeners-insertion).
2. `open_file` [click event](#listeners-clickevent).
3. `suggest_command` [click event](#listeners-clickevent).

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="feature-signs">Signs</a>

Signs allow usage of the text component through the `Text1, Text2, Text3, Text4` string tags. In order for any text on the sign to be valid and remain after reloading, all four tags **must** exist and **must** contain a valid text component.

Because the NBT data must be string data, quotation marks must be used as other characters would invalidate the JSON object. This also means that any nested quotation marks must be [escaped](#unicode).

```
/setblock ~ ~1 ~ minecraft:standing_sign 0 replace {Text1:"[\"Top text\"]",Text2:"[\"\"]",Text3:"[\"\"]",Text4:"[\"Bottom text\"]"}
```

**Unavailable features:**

1. `insertion` [event listener](#listeners-insertion).
2. `open_file` [click event](#listeners-clickevent).
3. `open_url` [click event](#listeners-clickevent).
4. `suggest_command` [click event](#listeners-clickevent).
5. `change_page` [click event](#listeners-clickevent).
6. **All** [hover events](#listeners-hoverevent).

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="feature-advancements">Advancements</a>

Advancements can use the text component in their `title` and `description` keys. Since advancements themselves use the JSON format, there is no need to encase the entire text component in quotation marks.

```
{
    "display": {
        "title": {"text":"This is the title.","color":"blue"},
        "description": {"translate":"custom.locale.key","with":[{"keybind":"key.inventory"}]},
        "icon": {
            "item": "minecraft:crafting_table"
        }
    },
    "criteria": {
        "custom_test_name": {
            "trigger": "minecraft:impossible"
        }
    }
}
```

**Unavailable features:**

1. [Score](#text-score) text.
1. [Selector](#text-selector) text.
2. **All** [event listeners](#listeners).

## In-game features

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text">Text input</a>

The first step to creating a text component is to specify the text to be shown. There are various methods to display text, but only one may be used at a time.

The order of precedence is as follows, from highest to lowest: `text`, `translate`, `score`, `selector`, `keybind`.

For example, if `translate` and `selector` are both defined at the same depth, `translate` will be used. This means `text` supersedes all.

One of these keys **must** be defined for the text component to be valid, though does not need to contain text.

```
/tellraw @a {"text":""}
```

Alternatively, if within an array of text components (such as from instantiation or via `extra`), a lone string will act as `text`.

```
/tellraw @a ["String 1",{"text":"","extra":["String 2"]}]
```

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text-text">`text`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Basic text with no parsing other than escape sequence support. Target selectors are **not** parsed using this option.

```
/tellraw @a {"text":"String"}
```
![](http://www.skylinerw.com/images/textcomponent/text1.png)

If specifying a lone string within an array of text components (such as from instantiation or via `extra`), the input will be parsed as the `text` option.

```
/tellraw @a ["String 1","String 2"]
/tellraw @a {"text":"String 1","extra":["String 2"]}
```
![](http://www.skylinerw.com/images/textcomponent/text2.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text-translate">`translate`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Sends input through the translation parser, useful for multi-lingual support. The input is expected to be the language key (e.g. `gui.toTitle`), but if the key is invalid it will be parsed as though it were the key's value instead. The output shown will be dependent on the player's language setting, unless the key is invalid.

```
/tellraw @a {"translate":"gui.toTitle"}
```
![](http://www.skylinerw.com/images/textcomponent/translate1.png)

Example, showing that invalid key results in input being parsed as output.

```
/tellraw @a {"translate":"Text inserted here"}
```
![](http://www.skylinerw.com/images/textcomponent/translate2.png)

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text-translate-flags">Conversion flags & `with`</a>

Minecraft's language files support the usage of several conversion flags, which are placeholders for text. For example:

```
commands.generic.entity.invalidType=Entity type '%s' is invalid
```

`%s` is the placeholder, which is text to be inserted at a later point. While the language files support the use of a handful of different conversion flags, **all flags are converted to `%s` or `%#$s`**. See [here](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html) for a more in-depth explanation of placeholders, but be aware that Minecraft implements them manually and thus not all are available.

The `with` array will hold the extra data to be inserted. Example, where the `%s` placeholder is replaced with "Creeper":

```
/tellraw @a {"translate":"commands.generic.entity.invalidType","with":["Creeper"]}
```
![](http://www.skylinerw.com/images/textcomponent/translate3.png)

Conversion flags are also supported when specifying an invalid key, but be aware that the game is expecting **only** `%s` or `%#$s`. No other conversion flag will work for those cases.

```
/tellraw @a {"translate":"Insert a %s here.","with":["STRING"]}
```
![](http://www.skylinerw.com/images/textcomponent/translate4.png)

***

Below is a list of conversion flags, how they are interpreted, and how they are intended to be used.

| Flag  | Description |
| ------------- | ------------- |
| %s | The next string in the sequence inside `with`. For example, given the following:<br><br>`custom.key = Insert %s and %s, followed by %s.`<br>`/tellraw @a {"translate":"custom.key","with":["STRING1", "STRING2"]}`<br>`Output = Insert STRING1 and STRING2, followed by .`<br><br>The first `%s` will grab the first record within `with`, being "STRING1". The second `%s` will grab the second record, being "STRING2". The third will then grab the third record, but because there is none, will simply become blank. |
| %d | Intended to be an integer. Converted to `%s` upon reading the language files. |
| %f | Intended to be a float. Converted to `%s` upon reading the language files. |
| %#$s | A specific record inside `with`. The # is replaced by the incremental record number within the array. Note that this does not affect the sequence used by `%s`. For example, given the following:<br><br>`custom.key = Insert %1$s and %2$s, followed by %1$s and %s.`<br>`/tellraw @a {"translate":"custom.key","with":["STRING1", "STRING2"]}`<br>`Output = Insert STRING1 and STRING2, followed by STRING1 and STRING1.`<br><br>`%1$s` gets the first record, being "STRING1". `%2$s` grabs the second record, being "STRING2". The first record is then grabbed again. Finally, `%s` accesses the current string in its sequence, and since no other `%s` was used, it will grab the first record "STRING1". |
| %#$d | Intended to be a specific integer record. Converted to `%#$s` upon reading the language files. |
| %#$f | Intended to be a specific float record. Converted to `%#$s` upon reading the language files. |
| %.#f | Intended to be a float with a maximum number of decimal places. Converted to `%s` upon reading the language files. |
| %.#d | While technically possible with Minecraft's parser, does not represent anything. Converted to `%s` upon reading the language files. |

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text-translate-with">`with` & text components</a>

The records inside `with` may also be text components. Availability will vary between in-game features, as usual. For example, the following replaces `%s` with a parsed target selector:

```
/tellraw @a {"translate":"Nearest player: %s","with":[{"selector":"@p"}]}
```
![](http://www.skylinerw.com/images/textcomponent/translate6.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text-score">`score`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

Displays the value of a score based on a playerscore on the scoreboard. The `score` object holds three keys: `name`, `objective`, and `value`.

A bug (see [MC-56373](https://bugs.mojang.com/browse/MC-56373)) currently prevents full usage within hover events (`value` does work).

`name` is the name of the player stored on the scoreboard, which may be a "fake" player. It can also be a target selector that **must** resolve to 1 target, and may target non-player entities. With a book, `/tellraw`, or `/title`, using the wildcard `*` in place of a name/selector will cause all players will see their own score in the specified objective. **Signs cannot use the wildcard.**

`objective` is the objective to find the player's score from.

`value` stores the processed value so that the score does not need to be re-evaluated. If defined, this value is used instead of a processed score.

Example, where all players are shown the score of the nearest player in the "TEST" objective:

```
/tellraw @a {"score":{"name":"@p","objective":"TEST"}}
```
![](http://www.skylinerw.com/images/textcomponent/score1.png)

Example, where all players will see their own score in the "TEST" objective:

```
/tellraw @a {"score":{"name":"*","objective":"TEST"}}
```

Example, where a score is not processed due to `value` being present and will instead display "hello":

```
/tellraw @a {"score":{"name":"@p","objective":"TEST","value":"hello"}}
```

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text-selector">`selector`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

Processes a target selector into a pre-formatted set of discovered names, complete with [event listeners](#listeners) where applicable. Multiple targets may be obtained, with commas separating each one and a final "and" for the last target.

The resulting formatting cannot be overwritten. This includes all styling from team prefixes, `insertion` event for entity & player names, `clickEvents` for player names, and `hoverEvents` for entity & player names.

A bug (see [MC-56373](https://bugs.mojang.com/browse/MC-56373)) prevents it from working in hover events.

Example, assuming there are 3 creepers:

```
/tellraw @a {"selector":"@e[type=Creeper]"}
```
![](http://www.skylinerw.com/images/textcomponent/selector1.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="text-keybind">`keybind`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Shows the player their corresponding key for a keybind. The following is a list of valid keybinds:

```
"key.forward"
"key.left"
"key.back"
"key.right"
"key.jump"
"key.sneak"
"key.sprint"
"key.inventory"
"key.swapHands"
"key.drop"
"key.use"
"key.attack"
"key.pickItem"
"key.chat"
"key.playerlist"
"key.command"
"key.screenshot"
"key.togglePerspective"
"key.smoothCamera"
"key.fullscreen"
"key.spectatorOutlines"
"key.hotbar.1"
"key.hotbar.2"
"key.hotbar.3"
"key.hotbar.4"
"key.hotbar.5"
"key.hotbar.6"
"key.hotbar.7"
"key.hotbar.8"
"key.hotbar.9"
"key.saveToolbarActivator"
"key.loadToolbarActivator"
```

Example, showing the player their key for dropping an item (defaulting to Q):

```
/tellraw @a {"keybind":"key.drop"}
```
![](http://www.skylinerw.com/images/textcomponent/keybind1.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="styling">Text styling</a>

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="styling-color">`color`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Choose from a set of pre-determined colors that will be applied to the text. Defaults to "white". List of colors and their IDs:

![](http://www.skylinerw.com/images/json/examples/colors/colors.png)

Example, where the text is colored "red":

```
/tellraw @a {"text":"Hello","color":"red"}
```
![](http://www.skylinerw.com/images/textcomponent/color1.png)

"reset" may be used to reset the color to default.

```
/tellraw @a {"text":"Hello","color":"red","extra":[{"text":" there","color":"reset"}]}
```
![](http://www.skylinerw.com/images/textcomponent/color2.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="styling-bold">`bold`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Boolean; increases text thickness. Defaults to "false".

```
/tellraw @a {"text":"Hello","bold":true}
```
![](http://www.skylinerw.com/images/textcomponent/bold1.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="styling-italic">`italic`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Boolean; emphasises text. Defaults to "false".

```
/tellraw @a {"text":"Hello","italic":true}
```
![](http://www.skylinerw.com/images/textcomponent/italic1.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="styling-underlined">`underlined`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Boolean; underlines text. Defaults to "false".

```
/tellraw @a {"text":"Hello","underlined":true}
```
![](http://www.skylinerw.com/images/textcomponent/underlined1.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="styling-strikethrough">`strikethrough`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Boolean; adds a strikethrough in the middle of text. Defaults to "false".

```
/tellraw @a {"text":"Hello","strikethrough":true}
```
![](http://www.skylinerw.com/images/textcomponent/strikethrough1.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="styling-obfuscated">`obfuscated`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

Boolean; causes text to cycle through random letters (example: ![](http://www.skylinerw.com/images/json/examples/options/obfuscated.gif)). Defaults to "false".

```
/tellraw @a {"text":"Hello","obfuscated":true}
```

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="listeners">Event listeners</a>

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="listeners-insertion">`insertion`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)

When the player holds `SHIFT` and left-clicks text, the `insertion` event listener will fire. This will append text to the player's current chat input, provided their chat is open. This is restricted to `/tellraw` only.

```
/tellraw @a {"text":"Shift-click","insertion":"/say Hello"}
```

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="listeners-clickevent">`clickEvent`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

When the player left-clicks `/tellraw` or book text, or right-clicks on a sign, the `clickEvent` listener will fire.

For signs, all `clickEvents` must be at the root of [inheritance](#inheritance). Clicking on a sign activates the listener for all of the `Text1/Text2/Text3/Text4` tags, thus the maximum number of `clickEvents` on a sign is 4.

The event is stored within a `clickEvent` object, with an `action` string holding the type of event and a `value` holding the various relevant values that corresponds with the action.

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="listeners-clickevents">List of clickEvents</a>

The following is a list of all possible clickEvents that can be used.

1. [`open_url`](#open_url)
2. [`open_file`](#open_file)
3. [`run_command`](#run_command)
4. [`suggest_command`](#suggest_command)
5. [`change_page`](#change_page)

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="open_url">`open_url`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)

Opens a URL using Java's [URI](https://docs.oracle.com/javase/7/docs/api/java/net/URI.html#URI%28java.lang.String%29) class. The only accepted protocols are "http" and "https", and one **must** be included. **Web links must also be enabled**.

```
/tellraw @a {"text":"Click","clickEvent":{"action":"open_url","value":"http://google.com"}}
```

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="open_file">`open_file`</a>

**Cannot be used with commands**. Opens a file on the clicking player's hard-drive. It is used in-game when taking a screenshot and clicking on the link provided.

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="run_command">`run_command`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

Runs a command with the clicking player set as the sender.

For `/tellraw` and books, **the clicking player is running the command as if they have typed the command themselves in the chat**. This means they are subject to standard chat limitations, being a 256-character limit, required "/" for command usage, as well as the requirement of OP status to run OP-only commands. The player will also be kicked if using an illegal character, such as the section symbol (`\u00A7`) and the DEL control character (`\u007F`). If the command to run exceeds 256 characters, the remainder will be trimmed and the player will still attempt to run the command. The [`/trigger`](#command-trigger) command was introduced to overcome these obstacles.

```
/tellraw @a {"text":"Click","clickEvent":{"action":"run_command","value":"/say Must be OP'd to run this command"}}
```

Signs will run commands themselves while setting the clicking player as the command sender, allowing players to run commands without standard chat limits as well as having sender bias apply to themselves. Note, however, that the coordinate origin of execution is still at the sign, so commands like `/setblock` will be run at the sign. This allows a player to modify the sign they click without having to know exactly where the sign is.

```
/setblock ~ ~1 ~ minecraft:standing_sign 0 replace {Text1:"{\"text\":\"\",\"clickEvent\":{\"action\":\"run_command\",\"value\":\"say Does not need to be OP'd to run this command. Sender bias: @e[c=1] .\"}}",Text2:"[\"\"]",Text3:"[\"\"]",Text4:"[\"\"]"}
```

In terms of CommandStats, signs will be the one receiving return values and not the player. The sign would need to run `/execute`, which will activate CommandStat triggers for both the sign and the player. Each event will run one at a time, obtaining the return value, and then moving onto the next event if there is one (starting at `Text1` and ending at `Text4`). This can allow for some complex command handling based on the sign's success at running commands.

For example, given the following command:

```
/setblock ~ ~1 ~ minecraft:standing_sign 0 replace {CommandStats:{SuccessCountName:"@p",SuccessCountObjective:"OBJ"},Text1:"{\"text\":\"\",\"clickEvent\":{\"action\":\"run_command\",\"value\":\"testfor @e[r=3]\"}}",Text2:"[{\"text\":\"\",\"clickEvent\":{\"action\":\"run_command\",\"value\":\"execute @p[score_OBJ_min=3] ~ ~ ~ say 3 entities around the sign.\"}}]",Text3:"[\"\"]",Text4:"[\"\"]"}
```

When the sign runs commands, it will set the nearest player's "OBJ" score equal to the success of the command. In `Text1`, the sign will run `/testfor` at its coordinate location, looking for any entities within 3 blocks of itself. It will then set the nearest player's score equal to that amount. In `Text2`, the sign will cause the nearest player with an "OBJ" score of at least 3 to run a `/say` command. That player's score will only be 3+ if there were 3+ entities around the sign. It will then set the nearest player's score to either 1 if the sign successfully ran `/execute`, or 0 if it did not.

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="suggest_command">`suggest_command`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)

Replaces the player's current chat input with the text in `value`. Unlike [`insertion`](#listeners-insertion), it completely replaces instead of appends.

```
/tellraw @a {"text":"Click","clickEvent":{"action":"suggest_command","value":"Text replaced"}}
```

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="change_page">`change_page`</a> ![](http://www.skylinerw.com/images/json/icons/book.png)

Used with books to switch to the page specified in `value`. If page number does not exist, nothing will happen.

```
/give @p minecraft:written_book 1 0 {title:"",author:"",pages:["{\"text\":\"Go to Page 2\",\"clickEvent\":{\"action\":\"change_page\",\"value\":\"2\"}}","[\"Page 2\"]","[\"Page 3\"]"]}
```

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="listeners-hoverevent">`hoverEvent`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)

When the player hovers over `/tellraw` or book text with their mouse pointer, the `hoverEvent` listener will fire.

The event is stored within a `hoverEvent` object, with an `action` string holding the type of event and a `value` holding the various relevant values that corresponds with the action.

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="listeners-hoverevents">List of hoverEvents</a>

The following is a list of all possible hoverEvents that can be used.

1. [`show_text`](#show_text)
2. [`show_achievement`](#show_achievement)
3. [`show_item`](#show_item)
4. [`show_entity`](#show_entity)

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="show_text">`show_text`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)

Shows a tooltip populated by a text component, though could also just be a simple string.

**Unavailable features:**

1. **All** [event listeners](#listeners).
2. [`score`](#text-score) text, though an explicit `value` will work.
3. [`selector`](#text-selector) text.

See [MC-56373](https://bugs.mojang.com/browse/MC-56373) for issues concerning `score` and `selector`.

```
/tellraw @a {"text":"Hover","hoverEvent":{"action":"show_text","value":"Basic string"}}

/tellraw @a {"text":"Hover","hoverEvent":{"action":"show_text","value":["",{"text":"Text\n","color":"green","underlined":true},"component"]}}
```
![](http://www.skylinerw.com/images/textcomponent/show_text1.png) ![](http://www.skylinerw.com/images/textcomponent/show_text2.png)

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="show_achievement">`show_achievement`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)

Displays a pre-formatted achievement tooltip. May also show pre-formatted statistics tooltips, though will only show the statistic name. The wiki has a list of valid achievement IDs [here](http://minecraft.gamepedia.com/Achievements#List_of_achievements), as well as statistic IDs [here](http://minecraft.gamepedia.com/Statistics#List_of_general_statistics).

Achievements will be prefixed by "achievement." while statistics are prefixed by "stat.".

```
/tellraw @a {"text":"Hover","hoverEvent":{"action":"show_achievement","value":"achievement.openInventory"}}

/tellraw @a {"text":"Hover","hoverEvent":{"action":"show_achievement","value":"stat.walkOneCm"}}
```
![](http://www.skylinerw.com/images/textcomponent/show_achievement1.png) ![](http://www.skylinerw.com/images/textcomponent/show_achievement2.png)

The following shows the pre-formatted text template:

![](http://www.skylinerw.com/images/textcomponent/show_achievement3.png)

The first line will be the achievement or stat name, dependent on the string provided from `value`.

The second line will either show "Achievement" or "Statistic" depending on the language setting, which is translated from the following keys in the language file:

```
stats.tooltip.type.achievement=Achievement
stats.tooltip.type.statistic=Statistic
```

The third line will display a description, which is only used for achievements.

If the value is not a valid achievement or statistic, it will instead display "Invalid statistic/achievement!".

![](http://www.skylinerw.com/images/textcomponent/show_achievement4.png)

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="show_item">`show_item`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)

Parses NBT input as an item and displays the result.

The value is a string and must have nested quotation marks escaped. **Must** be valid NBT input starting with an unnamed compound. Note that if Advanced Tooltips is shown (`F3 + H`), extra data will be shown as it normally would.

```
/tellraw @a {"text":"Hover","hoverEvent":{"action":"show_item","value":"{id:\"minecraft:stone\",tag:{display:{Lore:[\"Lore line 1\",\"Lore line 2\"]}}}"}}
```
![](http://www.skylinerw.com/images/textcomponent/show_item1.png) ![](http://www.skylinerw.com/images/textcomponent/show_item2.png)

If either the item data is invalid (wrong item ID, `Count` tag not 1+) or the NBT data is syntactically incorrect, "Invalid Item!" is shown instead.

![](http://www.skylinerw.com/images/textcomponent/show_item3.png)

##### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="show_entity">`show_entity`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)

An advanced tooltip displaying an entity's name, type, and UUID. Advanced tooltips **must** be enabled to view (`F3 + H`). Note that this is not targeting any existing entities, but is instead just creating dummy text.

The value is a string and must have nested quotation marks escaped. The value is NBT input of a specific structure, rather than entity data, but **none** of the following tags are required and can simply be blank:

```
{
    name:"CustomName",
    type:"ArmorStand",
    id:"00000000-0000-0000-0000-000000000000"
}
```

**None** of the tags are required, but the game will render a line for both `name` and `id`. `type` will only have a line dedicated to it if it's defined. The input does not need to be valid.

`name` is essentially the `CustomName` tag of the entity. `type` is the entity's savegame ID and will be appended by the corresponding numerical ID (90 (AKA Pig) if invalid). `id` is the UUID pair of the entity (determined by `UUIDLeast` and `UUIDMost` tags).

```
/tellraw @a {"text":"Hover","hoverEvent":{"action":"show_entity","value":"{name:\"Skylinerw\",type:\"Creeper\",id:\"00000000-0000-0000-0000-000000000000\"}"}}

/tellraw @a {"text":"Hover","hoverEvent":{"action":"show_entity","value":"{name:\"Skylinerw\",id:\"Not a valid UUID\"}"}}
```
![](http://www.skylinerw.com/images/textcomponent/show_entity1.png) ![](http://www.skylinerw.com/images/textcomponent/show_entity2.png)

If the NBT data is syntactically invalid, "Invalid Entity!" will be displayed instead.

![](http://www.skylinerw.com/images/textcomponent/show_entity3.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="adding-text">Adding text</a>

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="adding-text-extra">`extra`</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

The `extra` array will accept a list of text components and strings. This tag is used in order to create more text with their own options. Note that the text components here will inherit from a parent first, but each are their own separate child. See [Inheritance](#inheritance) for more info.

For example, the following results in the parent ("First") being red, while its children ("Second" and "Third") inherit that property and are also red. The first child ("Second") set itself to be bold, unlike its parent, while the second child ("Third") does not change any properties that it inherited.

```
/tellraw @a {"text":"First","color":"red","extra":[{"text":"Second","bold":true},"Third"]}
```
![](http://www.skylinerw.com/images/textcomponent/extra1.png)

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="adding-text-array">Array structure</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)

A text component can instead be instantiated as an array rather than an object. This essentially allows one to skip using the `extra` tag entirely, and can also save on characters as it becomes quicker to nullify a parent.

```
/tellraw @a ["First","Second","Third"]
```

Note that the first record defined will be marked as the parent, and all other records will inherit from that parent. For example, the following causes the first record ("Parent") to be the parent set to red, while the second record ("Child") inherits everything from the first record and will also be red.

```
/tellraw @a [{"text":"Parent","color":"red"},"Child"]
```
![](http://www.skylinerw.com/images/textcomponent/array1.png)

A quick way to skip over the first record as the parent to avoid any unwanted inheritance is to simply input an empty string.

```
/tellraw @a ["",{"text":"Child1","color":"red"},{"text":"Child2","underlined":true}]
```
![](http://www.skylinerw.com/images/textcomponent/array2.png)

## Additional usage info

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="inheritance">Inheritance</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)![](http://www.skylinerw.com/images/json/icons/advancement.png)[hr]

Inheritance refers to component options that are transferred from a parent/root component to a child/nested component. This is revalent when dealing with the `extra` tag or array instantiation. A child will take on all options from a parent, including styling and event listeners.

When instantiating as an object, the initial text is the parent. For example, the following shows a parent with no children.

```
/tellraw @a {"text":"Parent"}
```

The following shows a parent, with absolutely everything inside the `extra` tag being a child (including any "grandchildren" and so on).

```
/tellraw @a {"text":"Parent","extra":["Child1","Child2"]}
```

When instantiating as an array, the first record will be the parent while all other records are the children.

```
/tellraw @a ["Parent","Child1","Child2"]
```

Each child can be a parent to their own children, and only their children will inherit their properties (as well as any properties inherited by a grandparent and so on). The following shows a parent that is bold, a child that inherits boldness and has the italic property, and a grandchild that is both bold, italic, and has its own property of being red.

```
/tellraw @a {"text":"First","bold":true,"extra":[{"text":"Second","italic":true,"extra":[{"text":"Third","color":"red"}]}]}

/tellraw @a [{"text":"First","bold":true},{"text":"Second","italic":true,"extra":[{"text":"Third","color":"red"}]}]

/tellraw @a [{"text":"First","bold":true},[{"text":"Second","italic":true},{"text":"Third","color":"red"}]]
```
![](http://www.skylinerw.com/images/textcomponent/inherit1.png)

The following demonstrates that siblings do not inherit from one another. While the parent is bold and causes both its children to be bold, the first child will be italic while the second child is underlined (and not italic).

```
/tellraw @a {"text":"Parent","bold":true,"extra":[{"text":"Child1","italic":true},{"text":"Child2","underlined":true}]}

/tellraw @a [{"text":"Parent","bold":true},{"text":"Child1","italic":true},{"text":"Child2","underlined":true}]
```
![](http://www.skylinerw.com/images/textcomponent/inherit2.png)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="origins">Senders & origins</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)[hr]

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="origins-parsing">Initial parsing</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/title.png)![](http://www.skylinerw.com/images/json/icons/book.png)

When parsing the `selector` tag or the `name` tag in the `score` object, and if there is a command sender available, sender bias **will** apply, forcing them to always be the target.

This occurs when an entity runs a command (such as a player running the `/tellraw` or `/title` commands directly, or via the `/execute` command) or when the player opens a book for the first time.

For example, the following will cause all entities to say their own name no matter what:

```
/execute @e ~ ~ ~ /tellraw @a {"selector":"@e[c=1]"}
```

This functionality is not available for signs as they do not have a sender to work from, and instead their selectors will parse without a sender bias from the blockspace that they are placed at.

#### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="origins-clickevents">`run_command` clickEvents</a> ![](http://www.skylinerw.com/images/json/icons/tellraw.png)![](http://www.skylinerw.com/images/json/icons/book.png)![](http://www.skylinerw.com/images/json/icons/sign.png)

When a player activates a `run_command` event, they will be marked as the command sender as they are naturally the ones running the command. However, there is a key difference with signs: while `/tellraw` and books will use the player's current coordinates as the origin, clickEvents in signs will instead use the *sign's* coordinates as the origin, while **still** setting the clicking player as the command sender for sender bias to apply.

For example, the following will cause the **clicking** player to set a block above the sign, rather than above themselves, and to **always** say their own name:

```
/setblock ~ ~1 ~ minecraft:standing_sign 0 replace {Text1:"[{\"text\":\"\",\"clickEvent\":{\"action\":\"run_command\",\"value\":\"setblock ~ ~1 ~ minecraft:stone\"}}]",Text2:"[\"\"]",Text3:"[\"\"]",Text4:"[{\"text\":\"\",\"clickEvent\":{\"action\":\"run_command\",\"value\":\"say @e[c=1]\"}}]"}
```

This can be used very effectively in order to know exactly which sign the player had clicked while still being able to target the clicking player.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="command-trigger">Command: `/trigger`</a>

The `/trigger` command was introduced as a way around the various chat restrictions when running commands from `/tellraw` and book clickEvents. `/trigger` can be used by non-OPs and will be shorter than the 256-character limit.

Syntax:

```
/trigger [trigger-objective] [add|set] [value]
```

Players that run this command will **only** be capable of targeting their own objective, noted by the lack of a target selector in the syntax.

In order for the command to modify a score, the objective itself **must** use the "trigger" objective-type. This objective-type has a special feature in that it will be 'locked' in order to prevent being modified. This occurs when the value is modified by the `/trigger` command, though do note that if a player is not tracked in the objective they will still be considered 'locked'. Example:

```
/scoreboard objectives add OBJECTIVE trigger
/scoreboard players add @a OBJECTIVE 0
```

The player's "OBJECTIVE" score will be enabled by default in this case. Changing their score with `/scoreboard` does **not** unlock the objective for the player, but because the player had never run `/trigger` yet, it will be enabled.

The following will cause the player to change their score to 1, and thus locking the objective for that player. The player will not be able to run `/trigger` to modify that score until they are unlocked.

```
/trigger OBJECTIVE set 1
```

The `/scoreboard` command can be used to unlock the objective.

```
/scoreboard players enable @a OBJECTIVE
```

The `/execute` command can be used to forcibly change a player's score by causing them to run `/trigger`. This can be useful to automatically lock a player's objective without waiting for them to run the command themselves. For example, the following will not change their score but will lock it:

```
/execute @a ~ ~ ~ /trigger OBJECTIVE add 0
```

Example usage with `/tellraw`:

```
/tellraw @a {"text":"Click","clickEvent":{"action":"run_command","value":"/trigger OBJECTIVE set 1"}}
```

The clicking player will set their "OBJECTIVE" score to 1 if it is enabled for that player, and if so, will then become disabled.

## Conclusion

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="qa">Q&A</a>

> Can multiple clickEvents be run at the same time?

Only signs have this capability, and only 4 can run at a time. Click events on signs must be the root parent to function.

> Does the player activating a "run_command" clickEvent need to be OP'd?

Only for /tellraw and books. Signs do not require being OP'd. This requirement is useful as it can be used to differentiate between OP'd and non-OP'd players.

> What is the character limit for "run_command" clickEvents in /tellraw and books?

256; any extra text beyond will be trimmed off, which can also lead to errors in command syntax.

> Can I use target selectors as values?

Yes, you must use the "selector" tag, which replaces the "text" tag. The input can only be a target selector.

> Is JSON the same as NBT?

No, they are two different formats with different parsing rules.

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="external-links">External links</a>

**Bug reports**

- [MC-62866](https://bugs.mojang.com/browse/MC-62866) (JSON Inheritance in Books Different)
- [MC-56373](https://bugs.mojang.com/browse/MC-56373) (hoverEvent limitations)

**Other**

- [Text component generator](http://www.minecraftforum.net/forums/mapping-and-modding/minecraft-tools/1265406-website-to-create-books-signs-tellraw-commands-and) by [Ezfe](http://www.minecraftforum.net/members/ezfe)
- [Sign generator](http://www.minecraftforum.net/forums/mapping-and-modding/minecraft-tools/2253222-v1-0-2-3-500-dl-json-sign-generator-with-instant) by [CrushedPixel](http://www.minecraftforum.net/members/CrushedPixel)
- [Book generator](http://www.minecraftforum.net/forums/mapping-and-modding/minecraft-tools/2219621-v1-0-6-json-book-generator-easily-create-colored) by [CrushedPixel](http://www.minecraftforum.net/members/CrushedPixel)

**1.8 lenient to 1.9 strict conversion**

[Basic online tool](http://skylinerw.com/lenient/)
[MCEdit filter](http://www.minecraftforum.net/forums/mapping-and-modding/minecraft-tools/2601151-mcedit-filter-1-8-to-1-9-json-and-playsound) by [Al_T](http://www.minecraftforum.net/members/Al_T)

### [![Top](http://www.skylinerw.com/images/json/icons/top.png)](#table-of-contents) <a name="conclusion">Conclusion</a>

If there is outdated, missing, or incorrect information, please create a new issue.
