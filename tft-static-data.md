# Static Data
There is currently no static content that is automatically built for each patch of Teamfight Tactics. Many of the assets from Teamfight Tactics can be gathered from League of Legends' [Data Dragon](https://developer.riotgames.com//docs/lol#data-dragon). Below you can find some basic static data for Teamfight Tactic sets.

## Current Set
**Galaxies**
[set3.zip](https://static.developer.riotgames.com/docs/tft/set3.zip) contains static data for traits, champions, items, and galaxies.

### Traits
```json
[
  {
    "key": "Set3_Blademaster", // name used for traits in tft-match-v1
    "name": "Blademaster",
    "description": "Blademasters' Basic Attacks have a chance to trigger two additional attacks against their target. These additional attacks deal damage like Basic Attacks and trigger on-hit effects.",
    "type": "class",
    "sets": [
      {
        "style": "bronze",
        "min": 3,
        "max": 5
      },
      {
        "style": "gold",
        "min": 6,
        "max": 8
      },
      {
        "style": "chromatic",
        "min": 9
      }
    ]
  },
  ...
]
```

### Champions
```json
[
  {
    "name": "Ahri",
    "championId": "TFT3_Ahri", // championId used for units in tft-match-v1
    "cost": 2,
    "traits": [
        "Star Guardian",
        "Sorcerer"
    ]
  },
  ...
]
```

### Items
```
[
  {
    "id": 1,
    "name": "B.F. Sword"
  },
  ...
]
```

### Galaxies
```
[
  {
    "key": "TFT3_GameVariation_BigLittleLegends",
    "name": "Medium Legends",
    "description": "Little Legends are bigger and have +25 starting health."
  },
  ...
]
```

## Previous Sets
**Rise of the Elements**  
[set2.zip](https://static.developer.riotgames.com/docs/tft/set2.zip)
