---
title: "LCU API FAQ"
authors: "@RayZz-"
tags: "lcu-api"
markers: "generic"
---
# LCU API FAQ

## What is the LCU
The LCU, which stands for “League Client Update” is the League Client.

## What is the LCU API?
The LCU API are is collection of endpoints that the league clients renderer uses to display and modify information

## What can you do with the LCU API?
Anything you can do/see on the league client you can do with the LCU API.

## Anything I should know about using the LCU API?
- The LCU API is entirely unsupported and has no official documentation or support at all, most documentation/support is given by the community.

- It is important that you create an application on the [Riot Games developer portal]([https://developer.riotgames.com/](https://developer.riotgames.com/)) mentioning how you  plan to use the LCU in your project/application.

- The LCU API is locally hosted which means it cannot be accessed from outside the local users computer unless your application sends the information to your server.

- The LCU API uses a self signed certificate which most likely will cause issues, to get around the SSL/cert errors you can use the certificate available [here](https://static.developer.riotgames.com/docs/lol/riotgames.pem) or ignore SSL errors.

## Where do I get documentation for the LCU API

As stated above there is no official documentation or support provided by Riot Games for the LCU API,
however community made documentation exists,
below are some community made documentation for the LCU API.

*Note: You can get documentation from the LCU API through swagger and /help*

- [Rift explorer](https://github.com/Pupix/rift-explorer) - [Pupix](https://github.com/Pupix)
A desktop application which always has up to date documentation on the LCU API.
- [LCU Schema](http://mingweisamuel.com/lcu-schema/tool/) - [MingweiSamuel](https://github.com/MingweiSamuel)
An online version of the LCU  API's swagger
