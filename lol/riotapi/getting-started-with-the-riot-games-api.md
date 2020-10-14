---
title: "Getting Started With The Riot Games API"
authors: "@bangingheads"
tags: "beginners-guide,riotapi"
markers: "generic"
---
# Getting Started With The Riot Games API

This guide is to explain the basics of the Riot Games API for League of Legends. 

---
##  Before getting started
It is worthwhile to read over the developer policies and legal of Riot Games before starting on your project.
 - [Developer Policies](https://developer.riotgames.com/policies/general)
 - [Legal Jibber Jabber](https://www.riotgames.com/en/legal)

Although we can't cover it all, at the end of the day you can always [submit an application](https://developer.riotgames.com/app-type) and get the go ahead from Riot. 

It's probably not worth developing a project that they will reject, so you'll always want to read policies and get approval if it is in the grey area. We will cover applications later in the guide.

---
## Routing Values

The Riot API uses different hosts based on the region you are accessing.

### Platform Routing Values
Platform routing is used for all League of Legends endpoints other than the Tournament API endpoints.

|Region Code|Platform|Host
|--|--|--|
|BR|BR1|br1.api.riotgames.com
|EUNE|EUN1|eun1.api.riotgames.com
|EUW|EUW1|euw1.api.riotgames.com
|JP|JP1|jp1.api.riotgames.com
|KR|KR|kr.api.riotgames.com
|LAN|LA1|la1.api.riotgames.com
|LAS|LA2|la2.api.riotgames.com
|NA|NA1|na1.api.riotgames.com
|OCE|OC1|oc1.api.riotgames.com
|TR|TR1|tr1.api.riotgames.com
|RU|RU|ru.api.riotgames.com

### Regional Routing Values
Regional routing is used in the Tournament API / Tournament API stub.


|Region Codes|Region|Host
|--|--|--|
|BR, LAN, LAS, NA, OCE|AMERICAS|americas.api.riotgames.com
|JP, KR|ASIA|asia.api.riotgames.com
|EUNE, EUW, RU, TR|EUROPE|europe.api.riotgames.com

---
## Authentication
Now that we know where we are routing our requests, we will need to authenticate our requests.

### Generating an API Key
If you haven't already go to your [dashboard](https://developer.riotgames.com/) and generate a developer API Key. You will need to generate a new key every 24 hours. If you have already had an application approved you can use the API key from the app page.

### Authentication via Headers
The Riot API accepts your API key via headers. This is the cleaner method to authenticate.
The header is `X-Riot-Token`
For example:

    "X-Riot-Token": "RGAPI-b529Ax3f-3Y13-4A9d-a9Y2-10Ab9ecc8494"

### Authentication via Query Parameter
The Riot API also accept your API as a query parameter to the URL. This can become trickier as you may need to add extra query parameters to the URL as well.
The query parameter is `api_key`
An example URL authenticating by query parameter:

    https://na1.api.riotgames.com/lol/summoner/v4/summoners/by-name/TSMBjergsen?api_key=RGAPI-b529Ax3f-3Y13-4A9d-a9Y2-10Ab9ecc8494

---
## IDs
The API uses 3 types of IDs. These are summoner IDs, account IDs, and PUUIDs. Different APIs use/accept different types of IDs. 

IDs are encrypted on a per application basis. Your development application is considered an application. This means if you have any IDs stored from your development application you will have to grab them again if you get an approved personal/production application.

### Types of IDs

- **Summoner ID:** Regionally unique ID of a summoner. 
- **Account ID:** Regionally unique ID of an account.
- **PUUID:** Globally unique ID of an account.

Now you might ask, if PUUID is globally unique, why isn't it used on all endpoints? The back end for V4 was made before they released PUUID, all their newer games use it, so it will likely be used for all endpoints in V5. For now if you need to track players across regions you will have to call Summoner-V4 and store their PUUID.

As they are not unique across regions, summoner IDs and account IDs will change upon region transfer. They will return if the account returns to that region. Summoner name changes do not effect IDs.

---

## Example Usage of Riot API
Now that we know what IDs are, we will get an ID and use it in another endpoint as an example flow.

There are 3 primary places that you will get IDs: from a summoner name, from match data, and from leagues.

We can find a list of the available endpoints [here](https://developer.riotgames.com/apis).

In our example we want to get the current rank of `TSM Bjergsen` on the North American server. We will get his summoner ID through Summoner-V4's by-name endpoint.

We will build our URL, making sure to URL encode the name:

    https://na1.api.riotgames.com/lol/summoner/v4/summoners/by-name/TSM%20Bjergsen

We get a response as follows:

    {
        "id": "GNCF41eAxXVw7TVyVN84Jk7iWVUwN_R16ZGDtwS8-2NMpGQ",
        "accountId": "33yjTTkusyHOCSy3_4WU2kGJ0CyEesx9BydA9DN5U1Qj4pQ",
        "puuid": "WX9LmetKzMz6wKHE2pR8RMim8nmv9CV6vrWxww4rgBpKG9FnleQHRY7HycMhamfxazFd5rMBjT49kA",
        "name": "TSM Bjergsen",
        "profileIconId": 3271,
        "revisionDate": 1599420939000,
        "summonerLevel": 242
    }
  We will be using the `id` response. Although we need an `encryptedSummonerId`these are all already encrypted and this is coming from the Summoner endpoints, so this is an `encryptedSummonerId` You may notice some things in the API are called slightly different names in different endpoints, such as gameId and matchId as well.

Now that we have our ID we will pass it to League-V4's by-summoner endpoint.

We will build our URL:

    https://na1.api.riotgames.com/lol/league/v4/entries/by-summoner/GNCF41eAxXVw7TVyVN84Jk7iWVUwN_R16ZGDtwS8-2NMpGQ

We get the following response:

    [
        {
            "leagueId": "ca31c2ee-771c-453b-8527-76152d0c15fc",
            "queueType": "RANKED_SOLO_5x5",
            "tier": "DIAMOND",
            "rank": "II",
            "summonerId": "GNCF41eAxXVw7TVyVN84Jk7iWVUwN_R16ZGDtwS8-2NMpGQ",
            "summonerName": "TSM Bjergsen",
            "leaguePoints": 75,
            "wins": 405,
            "losses": 351,
            "veteran": false,
            "inactive": false,
            "freshBlood": false,
            "hotStreak": false
        }
    ]
We can successfully see Bjergsen is placed in 1 queue (1 element in the list) where he is Diamond 2 in Solo Queue. 

We have successfully gotten an ID and passed it to another endpoint! Other endpoints will follow a similar flow.

---

## Libraries
If you are new to the API it is recommended you use a library. These allow you to not have to worry about all the small intricacies of the API and be able to use more time building your project.

You can find a current list of them [here](https://riot-api-libraries.readthedocs.io/en/latest/libraries.html).

These are community made and most have detailed guides on how to use the library as well as include [rate limiting](https://www.hextechdocs.dev/lol/riotapi/12.rate-limiting) so you have less to worry about. They are built for all most common programming languages.

---

## Applications
Starting with the Riot API you will have a developer application. This API key expires every 24 hours. This is okay for development, but once you want to release your project you'll need something permanent.

Note: It is against developer policies to use a development key in production. It is irrelevant how long it takes to get approval, your application will be denied if they catch you using your development key in this manner.

### Personal Application
A personal application is for something that you are only using for yourself. This means you are controlling who has access. This would be ideal for a school project or a discord bot for your own server.

Personal Applications have the same rate limits as your development application, if you higher rate limits or if you plan on sharing your application you will have to apply for a production application.

### Production Application
A production application is for shareable applications. Production applications can vary in rate limits based on what Riot thinks your application needs to function.

Production applications need a website describing what the application does to function. If this is a web application you are already set. If not you can make a site using [GitHub Pages](https://pages.github.com/) or similar. You'll want to include screenshots and a description of what your application does so they can verify you are not leaking your API key or breaking policies.

### Application Process
Once you have finalized your idea you can apply for the type of application you need. Make sure you detail your application as best as you can. This will allow Riot to understand exactly what you are trying to achieve. The more built your prototype is, the better chance at better rate limits you have.

You can apply [here](https://developer.riotgames.com/app-type).

If you ever question whether or not something is allowed, you can submit an application and get Riot approval even if you don't have something built.

Current response time is around 20 business days but can vary due to other external factors at Riot. With that being said make sure you apply with plenty of time before your release date.
