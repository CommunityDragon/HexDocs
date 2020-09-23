---
title: "LoLEsports Match data"
authors: "@Canisback" 
tags: "esports,lolesports,data,match,games" # A comma separated list of the tags this document has
markers: "generic" # A comma separated list of all the markers this document has
---


# Gathering LoLEsports data

Following the shutdown of some important API from the lolesports website that allowed us to get hashes to gather data from official matches, the solution is to rely on Leaguepedia which has everything we need.

Here is an example how to do it in Python.

We will use the [leaguepdia-parser](https://pypi.org/project/leaguepedia-parser/) package to gather what we need from Leaguepedia.

## Find the tournament


```python
import leaguepedia_parser

lp = leaguepedia_parser.LeaguepediaParser()
lp.get_tournament_regions()
```




    ['',
     'Africa',
     'Asia',
     'Brazil',
     'China',
     'CIS',
     'Europe',
     'International',
     'Japan',
     'Korea',
     'LAN',
     'LAS',
     'Latin America',
     'LMS',
     'MENA',
     'North America',
     'Oceania',
     'PCS',
     'SEA',
     'Turkey',
     'Unknown',
     'Vietnam',
     'Wildcard']



Pick your favorite region to get the list of tournaments in this region : 


```python
tournaments = lp.get_tournaments('Europe', year=2020)
[t["name"] for t in tournaments]
```




    ['LEC 2020 Spring',
     'LEC 2020 Spring Playoffs',
     'EU Face-Off 2020',
     'LEC 2020 Summer',
     'LEC 2020 Summer Playoffs']



We will create a custom method for the leaguepedia_parser to get only the information we need : 


```python
import types

def get_games_hashes(self, tournament_name=None, **kwargs):
    """
    Returns the list server, gameId and hashes of games played in a tournament.

    :param tournament_name
                Name of the tournament, which can be gotten from get_tournaments().
    :return:
                A list of game dictionaries.
    """
    games = self._cargoquery(tables='ScoreboardGames',
                             fields='Tournament = tournament, '
                                    'MatchHistory = match_history_url, ',
                             where="ScoreboardGames.Tournament='{}'".format(tournament_name),
                             order_by="ScoreboardGames.DateTime_UTC",
                             **kwargs)
    data = [
        {
            "tournament":game["tournament"],
            "server":game["match_history_url"].split("/")[5],
            "gameId":game["match_history_url"].split("/")[6].split("?gameHash=")[0],
            "hash":game["match_history_url"].split("/")[6].split("?gameHash=")[1],
        }
        for game in games
    ]
    return data

lp.get_games_hashes = types.MethodType(get_games_hashes, lp)
```

## Getting the hashes

Getting the hashes for LEC 2020 Summer : 


```python
games = lp.get_games_hashes(tournaments[3]['name'])
games[:3]
```




    [{'tournament': 'LEC 2020 Summer',
      'server': 'ESPORTSTMNT04',
      'gameId': '1230688',
      'hash': '25cb7e1966cbcdb5'},
     {'tournament': 'LEC 2020 Summer',
      'server': 'ESPORTSTMNT04',
      'gameId': '1220706',
      'hash': 'c3f45e5bb2a65c80'},
     {'tournament': 'LEC 2020 Summer',
      'server': 'ESPORTSTMNT04',
      'gameId': '1220728',
      'hash': '4bfd5c00f9292be3'}]



Requesting the match data from all those games : 


```python
import requests

base_match_history_stats_url = "https://acs.leagueoflegends.com/v1/stats/game/{}/{}?gameHash={}"
base_match_history_stats_timeline_url = "https://acs.leagueoflegends.com/v1/stats/game/{}/{}/timeline?gameHash={}"

all_games_data = []

for g in games:
    url = base_match_history_stats_url.format(g["server"],g["gameId"],g["hash"])
    timeline_url = base_match_history_stats_timeline_url.format(g["server"],g["gameId"],g["hash"])
    
    game_data = requests.get(url).json()
    game_data["timeline"] = requests.get(timeline_url).json()
    
    all_games_data.append(game_data)
```

## In need of Cookies

ACS is highly rate limited and you might need to authenticate yourself to avoid this.

For that, you will need to : 
 * Open your browser
 * Log in League of Legends website (or any one dependent on Riot log in)
 * Open an acs link (e.g. https://acs.leagueoflegends.com/v1/stats/game/ESPORTSTMNT06/1070986?gameHash=1787d1ea36a382c3 )
 * Right clic -> Inspect element
 * Network tab
 * Select the first item in the network list (might need a refresh with the tab open)
 * Find the request header
 * Copy the value of the Cookie header

![ACS header](https://canisback.com/images/acs.png)

Paste this value in here : 


```python
cookies = ""
```


```python
import requests

base_match_history_stats_url = "https://acs.leagueoflegends.com/v1/stats/game/{}/{}?gameHash={}"
base_match_history_stats_timeline_url = "https://acs.leagueoflegends.com/v1/stats/game/{}/{}/timeline?gameHash={}"

all_games_data = []

for g in games:
    url = base_match_history_stats_url.format(g["server"],g["gameId"],g["hash"])
    timeline_url = base_match_history_stats_timeline_url.format(g["server"],g["gameId"],g["hash"])
    
    game_data = requests.get(url,  cookies={c.split("=")[0]:c.split("=")[1] for c in cookies.split(";")}).json()
    game_data["timeline"] = requests.get(timeline_url,  cookies={c.split("=")[0]:c.split("=")[1] for c in cookies.split(";")}).json()
    
    all_games_data.append(game_data)
```


```python
all_games_data[0]["participantIdentities"]
```




    [{'participantId': 1,
      'player': {'summonerName': 'G2 Wunder', 'profileIcon': 29}},
     {'participantId': 2,
      'player': {'summonerName': 'G2 Jankos', 'profileIcon': 29}},
     {'participantId': 3,
      'player': {'summonerName': 'G2 Caps', 'profileIcon': 29}},
     {'participantId': 4,
      'player': {'summonerName': 'G2 Perkz', 'profileIcon': 29}},
     {'participantId': 5,
      'player': {'summonerName': 'G2 Mikyx', 'profileIcon': 29}},
     {'participantId': 6,
      'player': {'summonerName': 'MAD Orome', 'profileIcon': 29}},
     {'participantId': 7,
      'player': {'summonerName': 'MAD Shad0w', 'profileIcon': 29}},
     {'participantId': 8,
      'player': {'summonerName': 'MAD Humanoid', 'profileIcon': 29}},
     {'participantId': 9,
      'player': {'summonerName': 'MAD Carzzy', 'profileIcon': 29}},
     {'participantId': 10,
      'player': {'summonerName': 'MAD Kaiser', 'profileIcon': 29}}]



Now you have all the match data you need, in the same format as the Riot Games API matches.

[Source notebook](https://canisback.com/notebooks/lolesports.ipynb)