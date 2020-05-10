---
title: "Getting started with the LCU websocket"
authors: "@thomasmarton"
tags: "beginners-guide,lcu,websocket"
markers: "generic"
---
# Getting started with the LCU websocket

Alongside the LCU REST API, the League Client Architecture uses a websocket connection to communicate changes from the LCU itself to the UX process to be shown to you (for example a friend request or a chat message you receive). This guide will show you how to connect to this websocket and request a notification about state changes.

This guide assumes that you have basic knowledge about the LCU API. Check out our other guide to help you get started.

---

The LCU websocket uses the WAMP 1.0 protocol on this websocket. An example implementation written by Pupix can be found [over here](https://gist.github.com/Pupix/eb662b1b784bb704a1390643738a8c15).

## Connecting to the socket

With the password and the port in your hands, this should be as easy as telling your websocket client/library to connect to `wss://127.0.0.1:12345/`. Keep in mind that you still have to pass in the authorization header as if you were calling an LCU endpoint.

If the connection was successful, it should stay open without any response, if anything went wrong, the only information you're given is the HTTP response code, but the only reason you might not be able to connect is if your header is not set properly.

## Communicating with the websocket

WAMP 1.0 uses a JSON array for every message. The first item of the array is always an opcode. `5` subscribes you to an event, `6` unsubscribes you from an event and `8` is the opcode the client will use when sending you an event.

### Subscribing to events

In order to subscribe to an event, you need to send a JSON array with a first index of 5, the opcode of the subscribe method and with the second index of a string telling the LCU which event you'd like to subscribe to.

For example sending `[5, "OnJsonApiEvent"]` would subscribe you to every event the client sends.
For a more complete list of events, you can call the `/help` endpoint provided by the client, `OnJsonApiEvent` will subscribe you to every available event.

### Unsubscribing from events

This is just as simple as subscribing, send an array with the first index of 6 and the second index being the name of the event you'd like to unsubscribe from, for example: `[6, "OnJsonApiEvent"]`

Keep in mind that you can't subscribe to OnJsonApiEvent and then unsubscribe from the smaller events you're not interested in. You have to subscribe to the smaller events in the first place.

### Receiving updates

Receiving updates is a bit different. The first 2 indexes of the array is the same as before, 8 will be the opcode, the second item will be the name of the event and the third item will be a JSON blob with 3 entries, `data`, `eventType` and `uri`. 

- uri will tell you the exact path that changed (it can be useful for events like chat where it includes identifiers)
- eventType will let you know the action that happened (`Create`, `Update`, `Delete`) on that path
- data is 100% specific to the event, but they generally follow the same structure their respective LCU endpoints use

Here's an example:

```json
[8,"OnJsonApiEvent",{"data":[],"eventType":"Update","uri":"/lol-ranked/v1/notifications"}]
```

Keep in mind that you'll receive an event twice if you subscribe to both `OnJsonApiEvent` and the respective smaller event so be careful with that.

## Conclusion

That's it! With this knowledge, you should be able to build reactive apps more easily compared to periodically polling the LCU API. If you have any questions, feel free to join Riot's 3rd party developer community on discord and ask your question there. There are a lot of knowledgeable members who are willing to answer them!