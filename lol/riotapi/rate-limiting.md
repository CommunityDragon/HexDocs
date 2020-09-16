---
title: "Rate Limiting"
authors: "@Canisback" 
tags: "rate-limiting,rate-limits" # A comma separated list of the tags this document has
markers: "generic" # A comma separated list of all the markers this document has
---

**Disclaimer** : this article consists in the old doc from the official dev portal :
https://web.archive.org/web/20190629194440/https://developer.riotgames.com/rate-limiting.html

## Rate Limiting 

#### What is Rate Limiting? 
In order to control the use of the Riot Games API, we set limits on how many times endpoints can be accessed within a given time period. These limits are put in place to minimize abuse, to maintain a high level of stability, and to protect the underlying systems that back the API from being overloaded. The underlying systems are the same systems that power League of Legends, so if they are overloaded, player experience suffers, and our first priority is to protect that experience. 

#### Rate Limiting Types 
There are three types of limits used in the API infrastructure - application, method and service rate limits. 

##### Application Rate Limits 
The first type of limit is enforced on a per API key basis and is called an **application rate limit**. App rate limits are enforced per region. Every call made to any Riot Games API endpoint in a given region counts against the app rate limit for that key in that region, except where noted on the [API Reference](https://developer.riotgames.com/api-methods) page. For example, calls to the static data API do not count against the app rate limit. If you have not already done so, please visit the [API Keys](https://developer.riotgames.com/api-keys.html) page for more information. An [example](#application-example) application rate limit response can be found below in the Rate Limit Examples section. 

##### Method Rate Limits 
The second type of limit is enforced on a per endpoint (or "method") basis for a given API key and is called a **method rate limit**. Method rate limits are enforced per region. Every call made to any Riot Games API endpoint in a given region counts against the method rate limit for the given method and API key in that region. An [example](#method-example) method rate limit response can be found below in the Rate Limit Examples section. 

##### Service Rate Limits 
The third type of limit is enforced on a per service basis and is called a **service rate limit**. Service rate limits are also enforced per region. Every call made to any endpoint for a given Riot Games API service in a given region counts against the service rate limit for that service in that region. When service rate limits apply, we will document them, including which endpoints are part of the rate limited service. Two example service rate limit responses can be found below in the Rate Limit Examples section. One [example](#service-example1) for a service rate limit enforced by the Riot Games API, and another [example](#service-example2) enforced by the underlying service. Do not confuse method rate limits for service rate limits. Method rate limits apply individually to each application. Service rate limits apply to the service, and are shared by all applications making calls to a service. These limits enforced by the API infrastructure are not the only gateways to the data provided. Some of the underlying services for certain endpoints may also implement their own rate limits, independently of the API infrastructure. In these cases, you will get a 429 error response, but there will be no `X-Rate-Limit-Type` header included in the response. Only when the rate limiting is enforced by the API edge infrastructure will this header be included. While it is our policy not to reveal the specifics of how our rate limiting works, you can assume for the purposes of your code that the bucket starts when you make your first API call. 

#### HTTP Headers and Response Codes
If a call exceeds the app or service rate limit for a given period of time, then subsequent calls made to limited endpoints will return a 429 "Rate limit exceeded" HTTP response until the rate limit expires. In addition to the response code, some additional headers will be included in the response that provide more information.

|Header&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|Meaning|Included|
|--- |--- |--- |
|X-Rate-Limit-Type|The rate limit type, either **method**, **service**, or **application**. **method** indicates you have exceeded the individual limits for that method. **application** indicates you have exceeded the total rate limit for your application. **service** is returned if the service is rate limiting it's connections from the Riot API layer, regardless of your API key's application or method rate limits.|Included in any 429 response where the rate limit was enforced by the API infrastructure. Not included in any 429 response where the rate limit was enforced by the underlying service to which the request was proxied.|
|Retry-After|The remaining number of seconds before the rate limit resets. Applies to both user and service rate limits.|Included in any 429 response where the rate limit was enforced by the API infrastructure. Not included in any 429 response where the rate limit was enforced by the underlying service to which the request was proxied.|
|X-App-Rate-Limit|The application rate limits currently being applied to your API key. See the [**Application Rate Limit Headers**](#application-headers) section below for more information.|Included in the response for all API calls that enforce an application rate limit. See the [**Application Rate Limit Headers**](#application-headers) below for more information.|
|X-Method-Rate-Limit|The method rate limits currently being applied to your API key. See the [**Method Rate Limit Headers**](#method-headers) section below for more information.|Included in the response for all API calls that enforce a method rate limit. See the [**Method Rate Limit Headers**](#method-headers) section below for more information.|
|X-App-Rate-Limit-Count|The number of calls to a specific method that have been made during a specific rate limit. See the [**Application Rate Limit Headers**](#application-headers) section below for more information.|Included in the response for all API calls that enforce a method rate limit. See the [**Application Rate Limit Headers**](#application-headers) section below for more information.|
|X-Method-Rate-Limit-Count|The number of calls to a specific method that have been made during a specific rate limit. See the [**Method Rate Limit Headers**](#method-headers) section below for more information.|Included in the response for all API calls that enforce a method rate limit. See the [**Method Rate Limit Headers**](#method-headers) section below for more information.|

<div id="application-headers"></div>

#### Application Rate Limit Headers 
The `X-App-Rate-Limit` and `X-App-Rate-Limit-Count` headers will be included in the response for all API calls that enforce an application rate limit. For example, calls to the lol-static-data-v1.2 endpoints will not include this header in the response because calls to that API are not rate limited. The `X-App-Rate-Limit` header contains a comma-separated list of each of the overall rate limits associated with your API key (most people have two; a 10 second rate limit and a 10 minute rate limit). The value of the header contains each of your API key's rate limits in a comma separated list. For each rate limit bucket, the value includes the number of calls your API key is allowed to make during that bucket and the duration of the bucket in seconds, separated by a colon. The `X-App-Rate-Limit-Count` header is very similar. The only difference is that for each rate limit bucket, the value includes the number of calls your API key has already made during that bucket, instead of the number of calls it is allowed to make. For example, let's say you have the following rate limits: 
* 100 calls per 1 second * 1,000 calls per 10 seconds 
* 60,000 calls per 10 minutes (600 seconds) 
* 360,000 calls per 1 hour (3,600 seconds) 

When you make your first call, the `X-App-Rate-Limit` and `X-App-Rate-Limit-Count` headers will contain the following.

```
X-App-Rate-Limit: 100:1,1000:10,60000:600,360000:3600 
X-App-Rate-Limit-Count: 1:1,1:10,1:600,1:3600
```

If you make a second call (3 seconds later) the X-App-Rate-Limit-Count header will return the following.

```
X-App-Rate-Limit: 100:1,1000:10,60000:600,360000:3600
X-App-Rate-Limit-Count: 1:1,2:10,2:600,2:3600
```

Notice how the first rate limit has reset because its time window is 1 second and how the second call still counts toward the other three rate limits. Here's another example.

```
X-App-Rate-Limit: 1000:10,60000:600
X-App-Rate-Limit-Count: 450:10,2000:600
```

The `X-App-Rate-Limit` header in this example shows this API key has a rate limit of 1,000 calls per 10 seconds and 60,000 calls per 600 seconds. The `X-App-Rate-Limit-Count` header shows that 450 API calls were made within the 10 second rate limit window and 2,000 API calls were made within the 10 minute (600 second) rate limit window. 

<div id="method-headers"></div>

#### Method Rate Limit Headers 
The `X-Method-Rate-Limit` and `X-Method-Rate-Limit-Count` headers are functionally identical to the The `X-App-Rate-Limit` and `X-App-Rate-Limit-Count` headers, except instead of reflecting the overall rate limits associated with your API key it reflects the per endpoint rate limits for your API key. Each endpoint in the API **can** and likely will have a different rate limit. The default rate limit for each method can be found in the table below, these rate limits may be overridden on a per API key basis.

|Method|Default Rate Limit|
|--- |--- |
|/lol/match/v3/matches/{matchId} <br />/lol/match/v3/timelines/by-match/{matchId}|500 requests / 10 seconds|
|/lol/match/v3/matchlists/by-account/{accountId}|1,000 requests / 10 seconds|
|champion-v3 endpoints |400 requests / 1 minute|
|/lol/league/v3/leagues/{leagueId} <br />/lol/league/v3/challengerleagues/by-queue/{queue} <br />/lol/league/v3/masterleagues/by-queue/{queue} <br />--- <br />leagues-v3 endpoints (GLOBAL)|<br /><br /><br /><br />500 requests / 10 seconds|
|/lol/league/v3/positions/by-summoner/{summonerId} <br />---<br /> leagues-v3 endpoints (EUW1)<br /> leagues-v3 endpoints (NA1)<br /> leagues-v3 endpoints (EUN1)<br /> leagues-v3 endpoints (BR1)<br /> leagues-v3 endpoints (KR)<br /> leagues-v3 endpoints (LA1)<br /> leagues-v3 endpoints (LA2)<br /> leagues-v3 endpoints (TR1)<br /> leagues-v3 endpoints (OC1)<br /> leagues-v3 endpoints (JP1)<br /> leagues-v3 endpoints (RU)|<br /><br />300 requests / 1 minute <br />270 requests / 1 minute <br />165 requests / 1 minute <br />90 requests / 1 minute <br />90 requests / 1 minute <br />80 requests / 1 minute <br />80 requests / 1 minute <br />60 requests / 1 minute <br />55 requests / 1 minute <br />35 requests / 1 minute <br />35 requests / 1 minute|
|/lol/summoner/v3/summoners/{summonerId} <br />/lol/summoner/v3/summoners/by-name/{summonerName} <br />--- <br />summoner-v3 endpoints (EUW1) <br />summoner-v3 endpoints (KR) <br />summoner-v3 endpoints (NA1) <br />summoner-v3 endpoints (EUN1) <br />summoner-v3 endpoints (BR) <br />summoner-v3 endpoints (TR1) <br />summoner-v3 endpoints (LA1) <br />summoner-v3 endpoints (LA2) <br />summoner-v3 endpoints (JP1) <br />summoner-v3 endpoints (OC1) <br />summoner-v3 endpoints (RU)|<br /><br /><br />2000 requests / 1 minute <br />2000 requests / 1 minute <br />2000 requests / 1 minute <br />1600 requests / 1 minute <br />1300 requests / 1 minute <br />1300 requests / 1 minute <br />1000 requests / 1 minute <br />1000 requests / 1 minute <br />800 requests / 1 minute <br />800 requests / 1 minute <br />600 requests / 1 minute|
|/lol/summoner/v3/summoners/by-account/{accountId} <br />---<br /> summoner-v3 endpoint (GLOBAL)|<br /><br />1000 requests / 1 minute|
|tournament-stub-v3 endpoints|10 requests / 10 seconds <br />500 requests / 10 minutes|
|All other endpoints|20,000 requests / 10 seconds|

<div id="application-example"></div>

#### Application Rate Limit Example

|API|Method|Path|
|--- |--- |--- |
|spectator-v3|GET_getFeaturedGames|/lol/spectator/v3/featured-games|
|spectator-v3|GET_getCurrentGameInfoBySummoner|/lol/spectator/v3/active-games/by-summoner/{summonerId}|


**Request URL**

<pre>https://na1.api.riotgames.com/lol/spectator/v3/featured-games</pre>

**Response Code**

<pre>429</pre>

**Response Headers**

<pre>Retry-After: 5
X-Rate-Limit-Type: application
X-App-Rate-Limit: 20:10
X-App-Rate-Limit-Count: 21:10
X-Method-Rate-Limit: 100:20
X-Method-Rate-Limit-Count: 1:20</pre>

From the information above, we have the following information: 
* The routing value for this API call was `na1`
* The method for this API call was `GET_getFeaturedGames`
* The service for this method is not indicated 
* The `X-Rate-Limit-Type` header shows we've exceeded our `application` rate limit 
* The `X-App-Rate-Limit`header shows our application rate limit is 20 calls every 10 seconds 
* The `X-App-Rate-Limit-Count` header shows we've exceeded our application rate limit by 1 API call 
* The `Retry-After` header shows our application rate limit will reset in 5 seconds 

From this information we can expect the following responses from the corresponding API calls:

|API Call|Expected Response|Explaination|
|--- |--- |--- |
|https://na1.api.riotgames.com/lol/spectator/v3/featured-games|429 Rate Limit Exceeded|We've exceeded our application rate limit for the na1 routing value.|
|https://la1.api.riotgames.com/lol/spectator/v3/featured-games|200 Successful|We've exceeded our application rate limit for the na1 routing value, but not the rate limit for the la1 routing value.|
|https://na1.api.riotgames.com/lol/spectator/v3/active-games/by-summoner/{summonerId}|429 Rate Limit Exceeded|We've exceeded our application rate limit for the na1 routing value.|
|https://la1.api.riotgames.com/lol/spectator/v3/active-games/by-summoner/{summonerId}|200 Successful|We've exceeded our application rate limit for the na1 routing value, but not the rate limit for the la1 routing value.|

<div id="method-example"></div>

#### Method Rate Limit Example

|API|Method|Path|
|--- |--- |--- |
|spectator-v3|GET_getFeaturedGames|/lol/spectator/v3/featured-games|
|spectator-v3|GET_getCurrentGameInfoBySummoner|/lol/spectator/v3/active-games/by-summoner/{summonerId}|


**Request URL**

<pre>https://la1.api.riotgames.com/lol/spectator/v3/featured-games</pre>

**Response Code**

<pre>429</pre>

**Response Headers**

<pre>Retry-After: 7
X-Rate-Limit-Type: method
X-App-Rate-Limit: 20:10
X-App-Rate-Limit-Count: 1:10
X-Method-Rate-Limit: 100:20
X-Method-Rate-Limit-Count: 105:20</pre>

From the information above, we have the following information:
* The routing value for this API call was `la1` 
* The method for this API call was `GET_getFeaturedGames`
* The service for this method is not indicated
* The `X-Rate-Limit-Type` header shows we've exceeded our `method` rate limit 
* The `X-Method-Rate-Limit` header shows our method rate limit is 100 calls every 20 seconds
* The `X-Method-Rate-Limit-Count` header shows we've exceeded our method rate limit by 5 API calls
* The `Retry-After` header shows our method rate limit will reset in 7 seconds 

From this information we can expect the following responses from the corresponding API calls:

|API Call|Expected Response|Explaination|
|--- |--- |--- |
|https://na1.api.riotgames.com/lol/spectator/v3/featured-games|200 Successful|We've exceeded our GET_getFeaturedGames method rate limit for the la1 routing value, but not the GET_getFeaturedGames rate limit for the na1 routing value.|
|https://la1.api.riotgames.com/lol/spectator/v3/featured-games|429 Rate Limit Exceeded|We've exceeded our GET_getFeaturedGames method rate limit for the la1 routing value.|
|https://na1.api.riotgames.com/lol/spectator/v3/active-games/by-summoner/{summonerId}|200 Successful|We've exceeded our GET_getFeaturedGames method rate limit for the la1 routing value, but not the GET_getCurrentGameInfoBySummoner method rate limit for the na1 routing value.|
|https://la1.api.riotgames.com/lol/spectator/v3/active-games/by-summoner/{summonerId}|200 Successful|We've exceeded our GET_getFeaturedGames method rate limit, but not the GET_getCurrentGameInfoBySummoner method rate limit.|

<div id="service-example1"></div>

#### Service Rate Limit Example 1

|API|Method|Path|
|--- |--- |--- |
|spectator-v3|GET_getFeaturedGames|/lol/spectator/v3/featured-games|
|spectator-v3|GET_getCurrentGameInfoBySummoner|/lol/spectator/v3/active-games/by-summoner/{summonerId}|


**Request URL**

<pre>https://na1.api.riotgames.com/lol/spectator/v3/featured-games</pre>

**Response Code**

<pre>429</pre>

**Response Headers**

<pre>Retry-After: 3
X-Rate-Limit-Type: service
X-App-Rate-Limit: 20:10
X-App-Rate-Limit-Count: 1:10
X-Method-Rate-Limit: 100:20
X-Method-Rate-Limit-Count: 5:20</pre>

From the information above, we have the following information: 
* The routing value for this API call was `na1` 
* The method for this API call was `GET_getFeaturedGames`
* The service for this method is not indicated 
* The `X-Rate-Limit-Type` header shows we've exceeded the `service` rate limit 
* The `Retry-After` header shows the service rate limit will reset in 3 seconds 

From this information we can expect the following responses from the corresponding API calls:

|API Call|Expected Response|Explaination|
|--- |--- |--- |
|https://na1.api.riotgames.com/lol/spectator/v3/featured-games|429 Successful|We've exceeded the service rate limit for the na1 routing value.|
|https://la1.api.riotgames.com/lol/spectator/v3/featured-games|200 Successful|We've exceeded the service rate limit for the na1 routing value, but not the rate limit for the la1 routing value.|
|https://na1.api.riotgames.com/lol/spectator/v3/active-games/by-summoner/{summonerId}|Unknown|It is unknown if the same service powers both spectator-v3 methods.|

<div id="service-example2"></div>

#### Service Rate Limit Example 2

|API|Method|Path|
|--- |--- |--- |
|spectator-v3|GET_getFeaturedGames|/lol/spectator/v3/featured-games|
|spectator-v3|GET_getCurrentGameInfoBySummoner|/lol/spectator/v3/active-games/by-summoner/{summonerId}|


**Request URL**

<pre>https://na1.api.riotgames.com/lol/spectator/v3/featured-games</pre>

**Response Code**

<pre>429</pre>

**Response Headers**

<pre>X-App-Rate-Limit: 20:10
X-App-Rate-Limit-Count: 1:10
X-Method-Rate-Limit: 100:20
X-Method-Rate-Limit-Count: 5:20</pre>

From the information above, we have the following information: 
* The routing value for this API call was <span class="code">na1</span> 
* The method for this API call was <span class="code">GET_getFeaturedGames</span> 
* The service for this method is not indicated 
* There is no <span class="code">X-Rate-Limit-Type</span> or <span class="code">Retry-After</span> header, so we can assume the rate limit was enforced by the underlying service and not the Riot Games API 

From this information we can expect the following responses from the corresponding API calls:

|API Call|Expected Response|Explaination|
|--- |--- |--- |
|https://na1.api.riotgames.com/lol/spectator/v3/featured-games|Unknown|We've exceeded the underlying service rate limit, but there is Retry-After header so we should back off exponentially until we receive a 200 response.|
|https://la1.api.riotgames.com/lol/spectator/v3/featured-games|Unknown|It is unknown if the underlying service rate limit is enforced across both the na1 and the la1 routing values.|
|https://na1.api.riotgames.com/lol/spectator/v3/active-games/by-summoner/{summonerId}|Unknown|It is unknown if the same service powers both spectator-v3 methods.|


#### Respect Rate Limits in Your Code 
You will want to make sure you're using your rate limit efficiently, which means designing your code to function under the rate limit (e.g., your code only ever will make a max of X calls per Y seconds, depending on your rate limit). There are many ways to achieve this goal, but one mechanism is to create a throttling queue where you add all the calls you need to make and a certain number get executed within a certain time frame. We provide all of the information you need about all of your rate limits in the response headers so that you can properly throttle your calls as well as back off for the appropriate amount of time when your application is rate limited. You should not hard-code throttle or rate limit values, as rate limits can change at any time. Use the headers and write your logic to respect what they are telling you. Note that if you use multiple threads all simultaneously making calls to the API, you will need to design your application properly so that your API key's rate limits are properly respected across threads to avoid blacklisting. For example, you can use a shared atomic counter across the threads to properly implement your throttling/rate limiting logic. If your application runs across multiple servers, there are still ways to leverage a shared atomic counter (e.g., redis). The level of precision that you chose to implement in your application is up to you, but know that your application risks being blacklisted if it routinely violates its rate limits. Even if you implement throttle queue logic in your application, when you do get back a 429 response, you should also still use the Retry-After header and implement back off logic to ensure that you prevent further rate limit violations. All the headers exist for a reason. If the rate limit was enforced by the underlying service to which the request was proxied, rather than the API edge, then the above headers will not be included. In that case, your code cannot use the same mechanism to handle these responses. Instead, your code would simply need to back off for a reasonable amount of time (e.g., 1 second) before trying again the same request. If you have questions about how to design or implement your application rate limiting properly, please hop into our [discord server](https://discord.gg/riotapi); there are plenty of friendly folks happy to help out. 

#### Tips to Avoid being Rate Limited
One of the worst experiences from the player's perspective is trying to use an awesome application that doesn't work. Regardless of what awesome experience you have created, the player will expect your application to function properly. That's why we recommend taking extreme care when crafting your code. Please note that some features you might want to provide are impossible with a rate limit, especially when it comes to the freshness of results. It is not required that you implement the tips provided below to obtain an approved production key. These tips are intended as best practices to improve your application's efficiency and avoid hitting your rate limit. 

##### Caching 
In addition to defensive programming, caching most, if not all, of the requests that your application makes will improve its performance. A local cache is especially helpful when many players request the same data over a short period of time (e.g., a pro player's recent games). In general, you should store API responses in your application or on your site if you expect a lot of use. For example, don't call the API every time a page on your website is loaded. Instead, load your page from a locally cached version of the API data. You can keep this cache updated by infrequent calls to the API that store the results in the cache. While there is more than one solution to the problem of caching, we are frequently asked to explain how it works. We encourage you to seek out and read tutorials or primers on caching before deciding on the solution that works best for you, but we provide an example here of how one could implement a local cache. One way to build a local cache is for the application to store each response it gets back from an API call in a local data store and assign a time after which this data becomes invalid or expires. For example, if you are storing match history and you want it to be updated frequently, 30 minutes is a reasonable expiry time, since a game lasts about 30 minutes on average. If you are storing information that doesn't change often, such as profile icons and summoner names, you could use a much longer expiry time, such as 24 hours. For information that only changes once per patch, such as game assets and resources returned by the static data API, you could make your expiry even longer. When your application requests information, it would first check your local cache to see if you have the requested data cached. If it isn't in cache, your application would fetch the data from the API and store it. If it is in cache, then your application would check if the data is still valid (hasn't expired). If the data has expired, your application would make an API call to refresh the information in cache. Note that for some use cases, the application might be better served by having a background thread that is refreshing expired data in cache, rather than checking it synchronously when a user makes a request to access data. 

##### Prioritize Active Users 
If your site keeps track of many players (for example, fetching their recent games or current statistics), consider only requesting data for players who have recently visited or signed into your site, or players that get looked up more frequently by your users. 

##### Adapt to Results 
If your application frequently queries for a set of players, you can introspect on the data to determine which players' data changes frequently and which change rarely. If some subset of players haven't had any change in their data for long periods of time, consider querying for their data less often or not at all. By using a back-off you can keep up to date on their data, but not waste cycles requesting data that very rarely changes. 

#### Blacklisting 
There are 2 ways you or your application can be blacklisted: 

##### Rate Limit Exceeded 
We ask that you honor the rate limit. If you or your application abuses the rate limits we will blacklist it. 

##### Unknown Resource Calls 
Please do not make calls to resources that do not exist. If you do we will blacklist your application If your application is blacklisted, all calls to the API with its API key will return a 403 response code, even if you regenerate the key. Blacklisting is enforced in phases. The first few times your application is blacklisted, it will be blacklisted only temporarily, but for a larger time period on each occurrence. After enough violations, the blacklisting will be permanent. If your application has been blacklisted and you think there has been an error you can submit an application note to address the issue, including the following information: 
* Explain why you think your application was blacklisted. 
* Describe in detail how you have fixed the problem that you think caused you to be blacklisted.