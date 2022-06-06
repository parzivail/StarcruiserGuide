---
layout: post
title: Debug Menu
subtitle: Datapad's Backstage
published: true
---

# Accessing the menu

In the response to the [`GAME_INIT` request](/message-loop/), the native backend has the opportunity to set the `debug` flag to `true`, which will prevnt console logging from being disabled, and allow access to the Datapad debug menu.

The debug menu is set up to be accessed in two ways, either by using a keyboard and pressing tilde (presumably for emulator use), or by using a gesture. The gesture can be activated by tapping this region 5 times within 1 second:

![The region you need to gesture within to access the debug menu](/images/main_menu__debug_tap_region.jpg)

The region is defined as the area contained by 40% and 60% of the view's width, and between 0% and 10% of the view's height.

| Page 1 | Page 2 |
| :-: | :-: |
| ![Page 1 of the debug menu](/images/debug_menu_page1.jpg) | ![Page 2 of the debug menu](/images/debug_menu_page2.jpg) |

# Page 1

| Button | Action |
| --- | --- |
| 📖2 | Show page 2 |
| Debug | Opens the Debug page |
| Agenda | Opens the Agenda page |
| Games | Opens the Games page |
| Jobs | Opens the Jobs page |
| TWAR | Opens the Territory War page |
| Ops Toggles | Opens the Ops Toggles page |
| Interactives | Opens the Interactives page |
| Beacons | Opens the Beacons page |
| Items | Opens the Items page |
| Save | Opens the Save page |
| Currency | Opens the Currency page |

# Page 2

| Button | Action |
| --- | --- |
| AudioShows... | Opens the AudioShows page |
| Filters... | Opens the Filters page |
| Park | Opens the Park page |
| Map | Opens the Map page |
| Hardware | Opens the Hardware page |
| Themes | Opens the Themes page |

# Page 1 → Debug page

| Button | Action |
| --- | --- |
| BLiS | Opens the Beacon Listening System page |
| DesiredFPS | Opens the Desired FPS page |
| Toggle Preview | Toggles the "preview overlay", an overlay showing `preview-overlay.png` onscreen, a 1080x1920 grayscale box gradient |
| Toggle Accessibility | Toggles the accessibility services (no visible result, likely screen reader support) |
| ✓ AR | Enables AR support |
| x AR | Disables AR support |
| Unlimit Disambigs | Un-caps the maximum number of beacons that can contribute to the Job/Hack/Tune/Translat search pages |
| Guest Experience Result | Forces an immediate query of the Guest Experience database, loading guest metadata regarding the Smuggler's Run attraction activities |

# Page 1 → Debug → Beacon Listening System page


| Button | Action |
| --- | --- |
| ✓ Fake Beacons | Makes the fake debugging beacons visible |
| x Fake Beacons | Makes the fake debugging beacons invisible |
| ✓ Ignore Checks | Disables range checking on beacons |
| x Ignore Checks | Enables range checking on beacons |
| Show | Shows a map of in-range beacons |