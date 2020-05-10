---
title: "Getting started with the LCU API"
authors: "@thomasmarton"
tags: "beginners-guide,lcu"
markers: "generic"
---
# Getting started with the LCU API

Ever since the new client came out and people found out that the League Client Update has a REST API that you can talk to, people have been hard at work figuring out what kind of amazing features they can make to supplement the out of game experience. This guide will show you how you can connect to this API, what endpoints are available and it'll include a short `things to know before getting started` section to make sure you know what you're getting into.

---

## Things you should definitely know before getting started

- The LCU API isn't supported by any means. We are allowed to use it, but things may change, completely disappear **at any point**! No warnings from Riot whatsoever, so keep in mind that you might have to do frequent updates if your specific endpoints are being worked on actively.
- It is only available locally, not even on your home network so don't treat it like the Riot API (it has CORS enabled so calling it from the browser isn't an option either)
- You're not allowed to use the LCU APIs to bypass the rate limiting that's enforced on the Riot Games API. If an API exists both in the Riot API and the LCU API, you must use the Riot API.
- You must register your LCU app on the developer portal even if you don't plan on using the Riot Games API.

With these out of the way, let's get started!

## Getting the port number and the password

The LCU API runs on your computer alongside many other programs so rather than using a fixed port and risking it not being available, the LCU just asks the operating system for an available port instead which means we're gonna have to find that port number. We have 2 solutions to that problem (both of them are going to cover the port number as well as the password):

### The lockfile method

When the client launches, it'll generate a file named `lockfile` in its installation directory (usually the C drive on Windows and the Applications folder on Mac **but it can change!**). If you happen to know the install location of League, you can proceed with this method, but I wouldn't recommend it due to the other method doing the same thing without asking the user to locate the install directory.
The lockfile consist of one line and it has 5 different pieces of data separated by the colon character. 
`Process Name : Process ID : Port : Password : Protocol`

It looks something like this:
```
LeagueClient:12345:54321:password:https
```
What we're after here is the 3rd and the 4th one. The 3rd one is the port number and the 4th one is the password.

### The process list method

While most people don't change where League of Legends gets installed, some might do so guessing that it might be on their C drive isn't always optimal. This method should be accurate no matter where their installation is as long as they have the client open. What we're gonna do here is query the process list of the operating system and search that process list for the UX process of the LCU. The UX process is responsible for actually displaying the client to you so it must know the port number and the password.
While I'm not gonna go into the exact details of coding this since each language is different, but all you have to do is execute a shell command on the host operating system. We're gonna use `ps` and `grep` for Mac and `wmic` for Windows:

On Windows this command looks like this: `wmic PROCESS WHERE name='LeagueClientUx.exe' GET commandline`
and here's the Mac counterpart: `ps -A | grep LeagueClientUx`

All you have to do is capture the results of these commands and do a regex search for `--app-port=` followed by a number (`--app-port=([0-9]*)`) and `--remoting-auth-token=` followed by a string (`--remoting-auth-token=([\w-_]*)`).

From here, you're ready to proceed with the password and the port ready to be used.

### Using a library

While not exactly a solution, but the community has made tons of amazing libraries for the LCU to help you make your idea a reality a bit faster. You can find a list of those libraries [here](https://github.com/CommunityDragon/awesome-league#developer-tools).

## Performing our first request

Now that we know what port we're going to be connecting to, let's make our first request and try to query who we are. We can find that under the lol-summoner plugin and the endpoint is `/lol-summoner/v1/current-summoner` so our full URL is going to look something like this: `https://127.0.0.1:12345/lol-summoner/v1/current-summoner` Keep in mind that the SSL certificate used by the LCU is self signed so you should make sure your software either trusts [Riot's root certificate](https://static.developer.riotgames.com/docs/lol/riotgames.pem) or it ignores that error.

### Finding the list of available endpoints

As I said before, the LCU API is completely unsupported so you shouldn't expect a fancy reference page from Riot directly. However the community has made some tools that you can use to see what's at your disposal:

- [Rift Explorer](https://github.com/Pupix/rift-explorer/) - A desktop application that connects directly to the client and fetches the list of every available endpoint
- [LCU schema](https://www.mingweisamuel.com/lcu-schema/tool/) - An online equivalent to Rift Explorer without the ability to call the APIs
- [CommunityDragon Raw](https://raw.communitydragon.org/) - A collection of every file that's part of the client allowing you to see how the client itself uses the LCU endpoints

### Connecting

The LCU API uses [HTTP Basic](https://en.wikipedia.org/wiki/Basic_access_authentication) authentication as a second layer of defense. The username is a constant, it's `riot` and the password changes with every client restart (alongside the port). HTTP Basic authentication BASICally concatenates the username, a colon and the password into a string and encodes it using base64. So the string `riot:password` becomes `cmlvdDpwYXNzd29yZA==`, let's put that into a header named `Authorization` and give it the value `Basic cmlvdDpwYXNzd29yZA==` to have a nice auth header. This is all you need to connect to the LCU API.

Here's a basic example showing all HTTP headers:

```
> GET /lol-summoner/v1/current-summoner HTTP/2
> Host: 127.0.0.1:12345
> Authorization: Basic cmlvdDpwYXNzd29yZA==
> User-Agent: insomnia/7.1.1
> Accept: */*

< HTTP/2 200 
< access-control-allow-origin: https://127.0.0.1:12345
< access-control-expose-headers: content-length
< vary: origin
< cache-control: no-store
< content-length: 382
< content-type: application/json
```

## Conclusion

That's all she wrote! With this knowledge, you should be able to find the port and the password to the LCU API and you're able to make requests to it. If you have any questions, feel free to join Riot's 3rd party developer community on discord and ask your question there. There are a lot of knowledgeable members who are willing to answer them!