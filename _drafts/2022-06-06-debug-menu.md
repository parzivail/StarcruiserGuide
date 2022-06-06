---
layout: post
title: Debug Menu
subtitle: Datapad's Backstage
---

# Accessing the menu

In the response to the [`GAME_INIT` request](/message-loop/), the native backend has the opportunity to set the `debug` flag to `true`, which will prevnt console logging from being disabled, and allow access to the Datapad debug menu.

The debug menu is set up to be accessed in two ways, one with a keyboard by pressing tilde (presumably for emulator use), and one with a gesture.