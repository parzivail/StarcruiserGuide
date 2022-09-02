---
layout: post
title: Tech Stack Overview
subtitle: Library and API notes
published: true
---

All information is relevant as of v2.19.0 [on Android](https://play.google.com/store/apps/details?id=com.disney.playdisneyparks.goo).

# Frontend

## Technology
The frontend of Datapad is built and packaged as a HTML5/JavaScript game that primarily utilizes the following libraries:

* [Phaser](https://phaser.io/), the main game engine that drives the UI and UX
* [three.js](https://threejs.org/), for handling AR experiences
* [Polyglot.js](https://airbnb.io/polyglot.js/), for internationalization and translation
* [NewRelic](https://newrelic.com/), for frontend analytics
* PlayAPI, a proprietary API for communicating with the Play Disney Parks application layer

## Notes
* The entire app is modularized with [RequireJS](https://requirejs.org/) and likely packed into the single-file release with [UglifyJS](https://github.com/mishoo/UglifyJS) through the RequireJS optimization tool. It's worth noting that the sources haven't been mangled or minified, but the comments have been removed for the entire application
  * Library files retain their comments
* While the build output is JavaScript (ES6), the source is likely in TypeScript due to the presence of the `__asyncValues` and `__asyncGenerator` helper functions.

# Backend

## Technology

The backend utilizes the following libraries (relevant to Datapad)

* [Firestore](https://firebase.google.com/docs/firestore), at least during development
  * While seemingly unused in the release build, an empty Firestore environment configuration file is included
  * Datapad data files, stored locally, also seem to be dumps of Firestore databases, with each data file using the following format:

```json-doc
{
	// ...
	"<Firestore random, unique document ID>": {
		// Document data
	}
	// ...
}
```

* [Project Griffon](https://aep-sdks.gitbook.io/docs/beta/project-griffon), from Adobe Experience Platform
* [Unity](https://unity.com/), for...?
  * I'm not sure where this is actually utilized, but the build contains a `libil2cpp.so` for `arm64-v8a` and `armeabi-v7a` targets, and disassembling them reveals that it seems to be a fairly fleshed-out C# reimplementation of the Java backend that handles the PlayAPI message loop (see below), as well as an example game, and some basic activities.
  * ~~It also contains subassemblies that reference minigames for "StormRider", presumably [the ride](https://disney.fandom.com/wiki/StormRider) at Tokyo Disneyland, but I can't figure out why — it closed in 2016 (two years before the app was released) and it would be the only reference to the app having any sort of functionality in Tokyo Disneyland~~
  * The Stormrider subassembly within the Unity application is likely the internal codename for the Disney Uncharted Adventure app

## Notes

The backend is likely mostly or completely written in Kotlin due to the high volume of Kotlin-compiler-generated features, stubs, and annotations present throughout the disassembly.

The frontend is served in a [WebView](https://developer.android.com/reference/android/webkit/WebView) that is notable in a few ways:

* WebSockets are supported (only secure ones)
  * Likely to support Project Griffon, since Datapad doesn't utilize this feature
* Hosts the bidirectional async message loop that the frontend PlayAPI uses to issue commands to the Android layer, or receive events from the Android layer
