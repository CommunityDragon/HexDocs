---
title: "Item Sets"
authors: "@Canisback" 
tags: "itemsets" # A comma separated list of the tags this document has
markers: "generic" # A comma separated list of all the markers this document has
---

**Disclaimer** : this article consists in the old doc from the official dev portal :
https://web.archive.org/web/20190629194439/https://developer.riotgames.com/item-sets.html


## Item Sets

An item set is a custom set of items that are displayed within the in-game item shop. They are included in a drop down along with the Recommended Items. Customized item sets can be added to the item shop by creating specially formatted JSON files in the League of Legends config.  

#### File Location

Item sets are stored within the League of Legends <span style="color:#FF9700; font-weight: bold;">config</span> directory. There are two paths for item sets depending on if they are global or champion specific. The file path for champion specific item sets is based on the key that is associated with each champion. The <span style="color:#0000FF; font-weight: bold;">champion key</span> can be found using **data dragon**.  

**Global**

<pre>League of Legends\<span style="color:#FF9700; font-weight: bold;">Config</span>\Global\Recommended\</pre>

**Champion Specific**

<pre>League of Legends\<span style="color:#FF9700; font-weight: bold;">Config</span>\Champions\<span style="color:#0000FF; font-weight: bold;">{championKey}</span>\Recommended\
<span style="color:#999;">League of Legends\Config\Champions\MonkeyKing\Recommended\</span></pre>

#### Reserved File Names

You can place any number of JSON files in the item set directories, however there are several reserved file names. The game will automatically replace any files that conflict with any of the following file names:  

<pre><span style="color:#0000FF; font-weight: bold;">{championKey}</span>SR.json
<span style="color:#0000FF; font-weight: bold;">{championKey}</span>TT.json
<span style="color:#0000FF; font-weight: bold;">{championKey}</span>DM.json
<span style="color:#0000FF; font-weight: bold;">{championKey}</span>ASC.json
<span style="color:#0000FF; font-weight: bold;">{championKey}</span>PG.json

<span style="color:#999;">// Example
MonkeyKingSR.json
MonkeyKingTT.json
MonkeyKingDM.json
MonkeyKingASC.json
MonkeyKingPG.json</span></pre>

## Item Set JSON

#### Item Set Details

<pre>{
    "title": <span style="color:#347C2C">"The name of the page"</span>,
    "type": <span style="color:#347C2C">"custom"</span>,
    "map": <span style="color:#347C2C">"any"</span>,
    "mode": <span style="color:#347C2C">"any"</span>,
    "priority": <span style="color:#b22222;">false</span>,
    "sortrank": <span style="color:#0000FF;">0</span>,
    "blocks": [
        <span style="color:#999;">...   // Array of blocks</span>
    ]
}</pre>

|||
|--- |--- |
|**title**|The name of the item set as you would see it in the drop down.|
|**type** <br />required|Can be `custom` or `global`. This field is only used for grouping and sorting item sets. Custom item sets are ordered above global item sets. This field does not govern whether an item set available for every champion. To make an item set available for every champion, the JSON file must be placed an item set in the global folder.|
|**map**<br />required|The map this item set will appear on. Can be `any`, Summoner's Rift `SR`, Howling Abyss `HA`.|
|**mode** <br />required|The mode this item set will appear on. Can be `any`, `CLASSIC`, `ARAM`, or Dominion `ODIN`.|
|**priority**|Selectively sort this item set above other item sets. Overrides **sortrank**, but not **type**. Defaults to `false`.|
|**sortrank**|The order in which this item set will be sorted within a specific **type**. Item sets are sorted in descending order.|
|**blocks**  <br />required|The sections within an item set.|


#### Block Details

<pre>"blocks": [
    {
        "type": <span style="color:#347C2C">"A block with just boots"</span>,
        "recMath": <span style="color:#b22222;">false</span>,
        "minSummonerLevel": <span style="color:#0000FF;">-1</span>,
        "maxSummonerLevel": <span style="color:#0000FF;">-1</span>,
        "showIfSummonerSpell": <span style="color:#347C2C">""</span>,
        "hideIfSummonerSpell": <span style="color:#347C2C">""</span>,
        "items": [
            <span style="color:#999;">...   // Array of items</span>
        ]
    },
    <span style="color:#999;">...   // Additional blocks</span>
]</pre>

|||
|--- |--- |
|**type** <br />required|The name of the block as you would see it in the item set.|
|**recMath**|Use the tutorial formatting when displaying items in the block. All items within the block are separated by a plus sign with the last item being separated by an arrow indicating that the other items build into the last item. Defaults to `false`.<br />![](https://web.archive.org/web/20190629194439im_/https://s3-us-west-1.amazonaws.com/riot-developer-portal/docs/recMath.png)|
|**minSummonerLevel**|The minimum required account level for the block to be visible to the player. Defaults to `-1` which is equivalent to "any account level."|
|**maxSummonerLevel**|The maximum allowed account level for the block to be visible to the player. Defaults to `-1` which is equivalent to "any account level."|
|**showIfSummonerSpell**|Only show the block if the player has equipped a specific summoner spell. Can be any valid summoner spell key, e.g. `SummonerDot`. Defaults to an empty string. Will not override **hideIfSummonerSpell**.  <br />Summoner spell data can be viewed through **data dragon**.|
|**hideIfSummonerSpell**|Hide the block if the player has equipped a specific summoner spell. Can be any valid summoner spell key, e.g. `SummonerDot`. Defaults to an empty string. Overrides **showIfSummonerSpell**.<br />Summoner spell data can be viewed through **data dragon**.|
|**items**  <br />required|An array of items to be displayed within the block.|


#### Item Details

<pre>"items": [
    {
        "id": <span style="color:#347C2C">"1001"</span>,
        "count": <span style="color:#0000FF;">1</span>
    },
    <span style="color:#999;">...   // Additional items</span>
]</pre>

|||
|--- |--- |
|**id**  <br />required|The item id as a string, e.g. `"1001"`.|
|**count**|The number of times this item should be purchased. The count is displayed in the bottom right of the item icon. The indicator counts down whenever the item is purchased. If the count reaches 0 the item will show a check mark indicating the item has been completed. Defaults to `0`.|


#### Example File

<pre>{
    "title": <span style="color:#347C2C">"The name of the page"</span>,
    "type": <span style="color:#347C2C">"custom"</span>,
    "map": <span style="color:#347C2C">"any"</span>,
    "mode": <span style="color:#347C2C">"any"</span>,
    "priority": <span style="color:#b22222;">false</span>,
    "sortrank": <span style="color:#0000FF;">0</span>,
    "blocks": [
        {
            "type": <span style="color:#347C2C">"A block with just boots"</span>,
            "recMath": <span style="color:#b22222;">false</span>,
            "minSummonerLevel": <span style="color:#0000FF;">-1</span>,
            "maxSummonerLevel": <span style="color:#0000FF;">-1</span>,
            "showIfSummonerSpell": <span style="color:#347C2C">""</span>,
            "hideIfSummonerSpell": <span style="color:#347C2C">""</span>,
            "items": [
                {
                    "id": <span style="color:#347C2C">"1001"</span>,
                    "count": <span style="color:#0000FF;">1</span>
                },
                <span style="color:#999;">...   // Additional items</span>
            ]
        },
        <span style="color:#999;">...   // Additional blocks</span>
    ]
}</pre>

You can download a sample item set JSON file here: [**https://s3-us-west-1.amazonaws.com/riot-developer-portal/docs/item-set.json**](https://web.archive.org/web/20190629194439/https://s3-us-west-1.amazonaws.com/riot-developer-portal/docs/item-set.json)