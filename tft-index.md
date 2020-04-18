# Teamfight Tactics
This document will provide you with a basic understanding of the Teamfight Tactics developer resources. It is designed to help you begin exploring and developing your own tools and products for Teamfight Tactics.

## Getting Started
Before starting on a product for Teamfight Tactics, there are **two requirements every developer must complete**:
1.  Read the [general policies](https://developer.riotgames.com//policies/general)
2.  [Register your product](https://developer.riotgames.com//app-type)

Only after developers have read the policies and registered their product on the Developer Portal, are they granted a limited license to use Riot Games’ intellectual property (IP) outlined in the [Legal Jibber Jabber](https://www.riotgames.com/en/legal).

## Routing Values
The first step to executing a request to the Teamfight Tactics API is to select the correct host to execute your request to. The Teamfight Tactics (TFT) API uses what we call routing values in the domain to ensure your request is properly routed. We use both platform ids and regions as routing values (e.g., na1 and americas). These routing values are determined by the topology of the underlying services. Services are frequently clustered by platform resulting in platform ids being used as routing values. Occasionally services are clustered by region, which is when regional routing values are used. The best way to tell if an endpoint uses a platform or a region as a routing value is to execute a sample request through the [reference page](https://developer.riotgames.com/apis).

### Platform Routing Values
|Platform|Host|
|--- |--- |
|BR1|br1.api.riotgames.com|
|EUN1|eun1.api.riotgames.com|
|EUW1|euw1.api.riotgames.com|
|JP1|jp1.api.riotgames.com|
|KR|kr.api.riotgames.com|
|LA1|la1.api.riotgames.com|
|LA2|la2.api.riotgames.com|
|NA1|na1.api.riotgames.com|
|OC1|oc1.api.riotgames.com|
|TR1|tr1.api.riotgames.com|
|RU|ru.api.riotgames.com|


### Regional Routing Values
|Region|Host|
|--- |--- |
|AMERICAS|americas.api.riotgames.com|
|ASIA|asia.api.riotgames.com|
|EUROPE|europe.api.riotgames.com|
