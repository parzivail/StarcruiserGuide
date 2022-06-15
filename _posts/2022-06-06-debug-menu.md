---
layout: post
title: Debug Menu
subtitle: Datapad's Backstage
published: true
---

# Accessing the menu

In the response to the [`GAME_INIT` request](/message-loop/), the native backend has the opportunity to set the `debug` flag to `true`, which will prevent console logging from being disabled, and allow access to the Datapad debug menu.

The debug menu is set up to be accessed in two ways, either by using a keyboard and pressing tilde (presumably for emulator use), or by using a gesture. The gesture can be activated by tapping this region 5 times within 1 second:

![The region you need to gesture within to access the debug menu](/images/main_menu__debug_tap_region.jpg)

The region is the area contained between 40% and 60% of the view's width, and between 0% and 10% of the view's height.

| Page 1 | Page 2 |
| :-: | :-: |
| ![Page 1 of the debug menu](/images/debug_menu_page1.jpg) | ![Page 2 of the debug menu](/images/debug_menu_page2.jpg) |

Yellow buttons trigger an action, while white buttons lead to "sub-pages" of the current page. Pressing "Back" at any time will bring you back to your previous page, or in the case of the first page, exit you from the debug menu. Pressing the close button immediately closes the menu.

# Page 1

| Button | Action |
| --- | --- |
| 📖2 | Show page 2 |
| Debug | Opens the Debug page |
| Agenda | Opens the Agenda page |
| Games | Opens the Games page |
| Jobs | Opens the Jobs page |
| TWAR | Opens the Territory War page |
| Ops Toggles | Opens the Operations Toggles page |
| Interactives | Opens a page where each park interactive show effects can be explicitly triggered, like the probe droid, Falcon, etc. |
| Beacons | Opens the Beacons page |
| Items | Opens the Items page |
| Save | Opens the Save page |
| Currency | Opens the Currency page |

# Page 2

| Button | Action |
| --- | --- |
| AudioShows… | Opens the AudioShows page |
| Filters… | Opens the Filters page |
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

# Page 1 → Debug → Desired FPS page

| Button | Action |
| --- | --- |
| 15 | Sets the Phaser game target FPS to 15 |
| 30 | Sets the Phaser game target FPS to 30 |
| 60 | Sets the Phaser game target FPS to 60 |

# Page 1 → Agenda page

| Button | Action |
| --- | --- |
| GetAgenda: PlayAPI | Disables agenda overrides |
| GetAgenda: Debug A | Forces the agenda to display the "A" override agenda |
| GetAgenda: Debug B | Forces the agenda to display the "B" override agenda |
| Multiply xN | Each press will increase N from its starting value of 1, though 10, and wrap back at 1. Entries in the agenda are duplicated N times in the view. |

# Page 1 → Games page

| Button | Action |
| --- | --- |
| UnscrambleHero | Opens the UnscrambleHero page |
| DeviceHack | Opens the DeviceHack page |
| DroidHack | Opens the DroidHack page |
| Tangrams | Opens the Tangrams page |
| SetDifficulty | Opens the SetDifficulty, which allows you to choose a forced game difficulty between 0 and 5 |
| Toggle Auto-Win | Toggles minigame auto-win |
| Toggle Show Difficulty | Toggles the visibility of the difficulty text in minigames |
| Toggle Adhoc Scan UnEncrypted | Force all crate manifest scans to be unencrypted |

# Page 1 → Games → UnscrambleHero page

| Button | Action |
| --- | --- |
| Diff _N_ | For each difficulty between 0 and 4, each button brings plays a test round of the audio tuning minigame |

# Page 1 → Games → DeviceHack page

| Button | Action |
| --- | --- |
| Diff _N_ | For each difficulty between 0 and 5, each button leads to a second page from which you can choose to play a specific level of difficulty _N_, or play a random one |

# Page 1 → Games → DroidHack page

| Button | Action |
| --- | --- |
| Diff _N_ | For each difficulty between 0 and 5, each button leads to a second page from which you can choose to play a specific level of difficulty _N_, or play a random one |

# Page 1 → Games → Tangrams page

| Button | Action |
| --- | --- |
| Diff _N_ | For each difficulty between 0 and 5, each button leads to a second page from which you can choose to play a specific level of difficulty _N_, or play a random one |

# Page 1 → Jobs page

| Button | Action |
| --- | --- |
| Chat Sequences | Opens a page which allows each chat sequence for each character to be directly initiated |
| View Unlisted | Toggles showing "unlisted" missions in the job list |
| Ignore Mission Requirements | Unused |

# Page 1 → Territory War page

| Button | Action |
| --- | --- |
| Sequences | Opens a page to start and end individual mock Territory War sequences, from 1 to 5, each with specific faction influences |
| Countdowns | Opens a page to set the Territory War countdown banner to display a countdown to moments 1, 15, 65, 600, and 6000 minutes in the future |
| Increment-Active | Increments the faction influence driven by the currently-running sequence by 0.1 |
| Reset all BLiPS | Fires mock events from each installation beacon using the current game state |
| Add-Hack-FO | Sets the mock influence of the First Order to 0.5 and the Resistance to 0.3 and installs a faction hack |
| Add-Hack-Res | Sets the mock influence of the First Order to 0.3 and the Resistance to 0.5 and installs a faction hack |

# Page 1 → Operations Toggles page

| Button | Action |
| --- | --- |
| RES JOBS | Opens a page where all Resistance jobs can be marked completed or incomplete |
| SM JOBS | Opens a page where all Smuggler jobs can be marked completed or incomplete |
| FO JOBS | Opens a page where all First Order jobs can be marked completed or incomplete |
| NEU JOBS | Opens a page where all Neutral jobs can be marked completed or incomplete |
| ✓ Landwide | Enables the "landwide" precondition, allowing all installations to be shown regardless of whether they're part of a mission |
| x Landwide | Disables the "landwide" precondition |
| ✓ On Planet | Marks the guest as being present in a Galaxy's Edge park |
| x On Planet | Marks the guest as being absent from a Galaxy's Edge park |
| ✓ On SWGS Ship | Marks the guest as being present onboard Galactic Starcruiser |
| x On SWGS Ship | Marks the guest as being absent from Galactic Starcruiser |
| Trigger Normal Mode | Puts the Datapad into the "normal" mode |
| Trigger SWGS Mode (Pre) | Puts the Datapad into the Galactic Starcruiser pre-sail mode |
| Trigger SWGS_S Mode (Sail) | Puts the Datapad into the Galactic Starcruiser on-voyage mode |
| Trigger Post SWGS Mode | Puts the Datapad into the Galactic Starcruiser post-sail mode |
| IGNORE TOGGLES | Globally overrides all other ops toggles to be `true` |
| USE TOGGLES | Removes the global override to all ops toggles |

# Page 1 → Beacons page

| Button | Action |
| --- | --- |
| _0-10_ | Opens a page where each fake BLiS beacon can be enabled or disabled, and in the case of Territory War-related beacons, increase/decrease faction influence and add/remove skimmers |
| ✓ Fake Beacons | Enable all debug BLiS beacons |
| x Fake Beacons | Disable all debug BLiS beacons |
| Turn On All Beacons | Adds all available beacons to the BLiS fake beacon list |

# Page 1 → Items page

| Button | Action |
| --- | --- |
| Achievements | Opens a page where either all achievements or a random one can granted, and achievement granting can be globally enabled or disabled |
| Transmission, InventoryItem, TransmissionStory, ShipLog, StarMapPiece, Program, Schematic, DroidMemory, SwgsCharacter | Opens a page where each specific item can be individually granted |
| RandomReward | Grants a random item |
| Award All | Grants all items |
| Remove All | Clears your inventory |

# Page 1 → Save page

| Button | Action |
| --- | --- |
| Clear Save State | Clears the SWGE save information, disables saving for this session, and exits the Datapad |
| Clear SWGS Chats | Removes all SWGS chat messages, resets all missions, resets the game state, disables saving for this session, and exits the Datapad |

# Page 1 → Currency page

| Button | Action |
| --- | --- |
| +1/10/100/1000 Credits | Adds a specific number of credits to your profile |
| -1/10/100/1000 Credits | Removes a specific number of credits from your profile |
| +1% FO/SM/RE | Adds 1% alignment to either your First Order, Smuggler or Resistance alignment |
| -1% FO/SM/RE | Removes 1% alignment from either your First Order, Smuggler or Resistance alignment |

# Page 2 → AudioShows… page

| Button | Action |
| --- | --- |
| Fake Tests… | Opens a page that enables emitting fake Audio Translate beacon payloads with no show specified at 480ms or 120ms intervals |
| _SHOW NAME_… | Opens a page where each Audio Translate show beacon can be configured to emit fake payloads |
| Enable | Globally enables Audio Translate shows throughout Datapad |
| Stop | Halts all fake packet emitting |

# Page 2 → Filters… page

| Button | Action |
| --- | --- |
| Proposed… | Opens a page where the "Proposed" filter can be set to low, mid, or high strength or disabled. |
| DirBlur… | Opens a page where the "DirBlur" filter can be set to low, mid, or high strength or disabled. |
| Distort… | Opens a page where the "Distort" filter can be set to low, mid, or high strength or disabled. |
| Noise… | Opens a page where the "Noise" filter can be set to low, mid, or high strength or disabled. |
| Chroma… | Opens a page where the "Chroma" filter can be set to low, mid, or high strength or disabled. |
| Bloom… | Opens a page where the "Bloom" filter can be set to low, mid, or high strength or disabled. |
| Tests… | Opens the Filters Tests page |
| 🗑 Disable All | Disables all applied filters |
| Cycle Filters| Incrementally changes the strength and combinations of different filters |

| Filter | Demo |
| Proposed (High) | |
| DirBlur (High) | |
| Distort (High) | ![](https://thumbs.gfycat.com/ZealousSatisfiedAtlanticsharpnosepuffer-size_restricted.gif) |
| Noise (High) | |
| Chroma (High) | |
| Bloom (High) | |