---
layout: post
title: Background
---

# What is the Datapad?

Launching in 2019, the Datapad is one of the activities included in the [Play Disney Parks app](https://disneyworld.disney.go.com/guest-services/play-app/), and grants guests an interactive experience inside the Galaxy's Edge parks, and more recently, the Galactic Starcruiser adventure.

It offers a number of interactive elements to guests throughout each park, including doing "jobs" for fictional characters in exchange for digital items and currency, collecting digital items around the park by scanning barcodes found on various props, and completing minigames that trigger audiovisual displays, like hacking droids or slicing door locks, at certain areas of the park.

# Why investigate it so deeply?

During a visit to Galactic Starcruiser, each member of my party took different "routes" through the story, and we all got to experience different interactions with characters and console terminals. I wondered if it would be possible to make a sort of "dialogue tree" that detailed all of the experiences you could have through the app, and what choices you would need to make to have them.

Parks at Disney, generally speaking, are "connectivity hostile". Geography, physical structures, and local interference sources (like other guests) often pose a significant threat to RF links, whether it's for a connection to a cellular network, WiFi, or something else.

Environments such as this require special attention to make sure devices can function with as few outages as possible, even while a guest is in an RF "dead zone". This essentially forces all of the logic and assets to be stored locally in the application instead of offloading it to a server. What that means for Datapad is that all of the logic and related data, like chat messages and minigames are all defined, stored, and processed locally.

# What is the StarcruiserGuide project?

After figuring out that everything was handled locally on a device, I set to work datamining the application to see what information we could glean from the interaction logic and definitions. Turns out, there's a lot to talk about, and the application itself is quite interesting, so I set up an outlet to document the process.