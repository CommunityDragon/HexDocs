# Contributing to HexDocs

### Folder structure

Our folder structure follows the same structure as the website so if you'd like to contribute to the Riot API category within League of Legends (URL is `/lol/riotapi`) then that's the path you should put the document in

### Naming conventions

We'd like to ask you to use a self-descriptive name as names cannot be changed at this moment. Think of the name of the file as the title of your document. The name has to end with `.md` and it can only contain English letters, numbers and spaces. This is both to define a convention and to help other contributors find the document they are looking for more easily.

### Content

The content of the file is standard markdown with a front matter on the top helping us keep metadata inside the markdown file as well. (The renderer we use is similar to the one GitHub uses so you can rely on it to see how your document will look like)

For content format, we'd like you to keep it professional and clean. Emojis are fine but we'd like to keep all documents focused on teaching you things you'd like to learn and keeping jokes and chatting to a [discord](https://discord.gg/a89DaHs) server for example. So please keep it minimalistic, clean and on-topic.

As for the language, we are only accepting documents written in English at the moment, but we are planning to expand this to other languages. Programming language is up to your discretion but if your document is focused more on code samples than a general solution, please note that in the title of your document (for example: `Calculating winrates with the Riot API using PHP`)

### Front matter

A front matter is a simple piece of YAML that appears on the top of every document. It helps us keep the metadata next to the document itself so we can let the community manage them rather than us having to.
Here's an example from the first document we've published here:

```yaml
---
title: "Using third-party-code to verify account ownership" # The title of the document
authors: "@thomasmarton" # Comma separated list of the public authors (if you don't want to be featured, just don't put your github handle in)
tags: "third-party-code" # A comma separated list of the tags this document has
markers: "lol-third-party-code" # A comma separated list of all the markers this document has
---
```

Neither of these fields can be missing. If you don't want to be featured and would like to make a new document, you can leave the string empty. But it has to be present.

### Markers

We use a system called `markers` to help you more easily find the exact document you're looking for within a category. These are specific and had to be registered by us (unlike tags which are 100% custom). Each marker is named after the specific API it represents so if your document talks about them, you should mark them accordingly. There might be some cases where your document doesn't talk about any APIs (like static data related documents), in that case, you can use the marker named `generic` to appear as an `Other document`

The current marker roster for League of Legends: `lol-champion-mastery`, `lol-champion`, `lol-clash`, `lol-league`, `lol-status`, `lol-match`, `lol-summoner`, `lol-spectator`, `lol-third-party-code`, `lol-tournament`
for Teamfight Tactics: `tft-league`, `tft-match`, `tft-summoner`
for Legends of Runeterra: `lor-ranked`
anything else: `generic`

*If you feel like we're missing a category, feel free to have a chat with us about adding it in*


### Making the pull request

Contributing happens via pull requests. When you're happy with your changes, submit a pull request to the repo and keep an eye on the comments feed as we might have some questions about your submission.

## Conclusion

aaaand that's all she wrote! We know we made it a bit too difficult to contribute to our repository but we wanted to keep the viewer experience as easy as possible (even at the cost of making contributions a bit more difficult). If you have any questions, feel free to come and chat with us on any of these 3 discord servers. We'd recommend using the last one for development purposes as it's more focused on that, but if you'd like to have a virtual coffee with us, we'd recommend joining the Celerity discord:

- [Riot Games 3rd party developer community](https://discord.gg/riotgamesdevrel)
- [Celerity discord](https://discord.gg/a89DaHs)
- [CommunityDragon discord](https://discord.gg/W8yzgTg)

Look for `DatThomaSs#4364` or `Ray.#8080` when joining the servers.
