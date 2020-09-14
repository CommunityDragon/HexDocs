---
title: "Maps data"
authors: "@Canisback" 
tags: "maps,map" # A comma separated list of the tags this document has
markers: "generic" # A comma separated list of all the markers this document has
---

**Disclaimer** : this article consists mostly in the old doc from the official dev portal :
https://web.archive.org/web/20190629194440/https://developer.riotgames.com/game-constants.html

## Match Timeline Data Position Values

Match timeline provides positions for various events. Here is a list of the maps and the bounds used for that.
Example in Javascript on how to use maps and positions : http://jsfiddle.net/ow4tsbne

| Name     |      Image      |  Notes |
|----------|:-------------:|:------|
| Summoner's Rift |  https://ddragon.leagueoflegends.com/cdn/10.18.1/img/map/map11.png <br />![map](https://ddragon.leagueoflegends.com/cdn/10.18.1/img/map/map11.png)| Map bounds : <ul><li>min: {x: -120, y: -120}</li><li>max: {x: 14870, y: 14980}</li></ul> |
| Twisted Treeline |  https://ddragon.leagueoflegends.com/cdn/10.3.1/img/map/map10.png <br />![map](https://ddragon.leagueoflegends.com/cdn/10.3.1/img/map/map10.png)| Map bounds : <ul><li>min: {x: 0, y: 0}</li><li>max: {x: 15398, y: 15398}</li></ul> |
| Howling Abyss |  https://ddragon.leagueoflegends.com/cdn/10.18.1/img/map/map12.png <br />![map](https://ddragon.leagueoflegends.com/cdn/10.18.1/img/map/map12.png)| Map bounds : <ul><li>min: {x: -28, y: -19}</li><li>max: {x: 12849, y: 12858}</li></ul> |
| Nexus Blitz |  https://ddragon.leagueoflegends.com/cdn/10.18.1/img/map/map21.png <br /><img src="https://ddragon.leagueoflegends.com/cdn/10.18.1/img/map/map21.png" width=512px/>| |


Additionnally, list of buildings : 

Summoner's Rift : 
```
{
    "BLUE_TOP_LANE_OUTER_TURRET":{"x":981,"y":10441},
    "BLUE_TOP_LANE_INNER_TURRET":{"x":1512,"y":6699},
    "BLUE_TOP_LANE_BASE_TURRET":{"x":1169,"y":4287},
    "BLUE_MID_LANE_OUTER_TURRET":{"x":5846,"y":6396},
    "BLUE_MID_LANE_INNER_TURRET":{"x":5048,"y":4812},
    "BLUE_MID_LANE_BASE_TURRET":{"x":3651,"y":3696},
    "BLUE_BOT_LANE_OUTER_TURRET":{"x":10504,"y":1029},
    "BLUE_BOT_LANE_INNER_TURRET":{"x":6919,"y":1483},
    "BLUE_BOT_LANE_BASE_TURRET":{"x":4281,"y":1253},
    "BLUE_TOP_LANE_NEXUS_TURRET":{"x":1748,"y":2270},
    "BLUE_BOT_LANE_NEXUS_TURRET":{"x":2177,"y":1807},
    "RED_TOP_LANE_OUTER_TURRET":{"x":4318,"y":13875},
    "RED_TOP_LANE_INNER_TURRET":{"x":7943,"y":13411},
    "RED_TOP_LANE_BASE_TURRET":{"x":10481,"y":13650},
    "RED_MID_LANE_OUTER_TURRET":{"x":8955,"y":8510},
    "RED_MID_LANE_INNER_TURRET":{"x":9767,"y":10113},
    "RED_MID_LANE_BASE_TURRET":{"x":11134,"y":11207},
    "RED_BOT_LANE_OUTER_TURRET":{"x":13866,"y":4505},
    "RED_BOT_LANE_INNER_TURRET":{"x":13327,"y":8226},
    "RED_BOT_LANE_BASE_TURRET":{"x":13624,"y":10572},
    "RED_TOP_LANE_NEXUS_TURRET":{"x":12611,"y":13084},
    "RED_BOT_LANE_NEXUS_TURRET":{"x":13052,"y":12612},
    "BLUE_TOP_LANE_INHIBITOR":{"x":1171,"y":3571},
    "BLUE_MID_LANE_INHIBITOR":{"x":3203,"y":3208},
    "BLUE_BOT_LANE_INHIBITOR":{"x":3452,"y":1236},
    "RED_TOP_LANE_INHIBITOR":{"x":11261,"y":13676},
    "RED_MID_LANE_INHIBITOR":{"x":11598,"y":11667},
    "RED_BOT_LANE_INHIBITOR":{"x":13604,"y":11316}
}
```
