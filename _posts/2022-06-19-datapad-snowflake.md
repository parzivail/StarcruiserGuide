---
published: true
layout: post
title: Snowflake IDs
subtitle: 'How Datapad makes use of articy:draft'
---
Every single "entity" in Datapad, including every item, beacon, map location, character, chat message, transmission, program, and droid memory, is assigned a unique ID that can be used to reference that entity elsewhere throughout Datapad's frontend. These IDs are not managed directly by the creative authors but are instead generated through [articy:draft](https://www.articy.com/en/), which we can infer is used by the creative team due to various references to Articy related constructs, like the asset partition directories being a subfolder of `articy`.

If Articy has a name for their ID format, I can't find it in their documentation. In this document I'll refer to it as the **Datapad Snowflake** format, since it encodes infomation in roughly the same way that the [Twitter](https://github.com/twitter-archive/snowflake) and [Discord](https://discord.com/developers/docs/reference#snowflakes) snowflakes do.

# Format

The snowflake is a 64-bit unsigned integer that representes three packed values. 

```
Type     User                     Serial
11111111 111111111111111111111111 11111111111111111111111111111111
64    57 56                    31 32                             0
```

| Field | Description |
| --- | --- |
| Type | The snowflake type, either Temporary (`0`, denotes data internal to Articy), Permanent (`1`, IDs that persist through saves and exports), Session Data (`2`), or Fixed (`3`) |
| User | The ID of the user that created the snowflake |
| Serial | A sequential number that increments every time a snowflake is generated |

# Statistics

As of Datapad 2.19.1, there are 11,038 unique snowflakes.

## Totals

| Type | Count |
| --- | --: |
| Permanent (`1`) | 11,038 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #68 | 3451 | 94 | 52981 |
| User #5 | 2052 | 423 | 88438 |
| User #23 | 1625 | 2266 | 44002 |
| User #7 | 906 | 19119 | 55574 |
| User #65 | 675 | 270 | 13615 |
| User #2 | 537 | 972 | 18175 |
| User #72 | 462 | 1 | 4218 |
| User #63 | 305 | 65 | 4392 |
| User #57 | 251 | 168 | 6379 |
| User #21 | 172 | 302 | 4332 |
| User #62 | 170 | 497 | 7763 |
| User #9 | 109 | 5629 | 6936 |
| User #32 | 77 | 179 | 1148 |
| User #12 | 56 | 3241 | 4766 |
| User #22 | 43 | 1 | 629 |
| User #8 | 36 | 752 | 1539 |
| User #0 | 30 | 6931 | 24335 |
| User #46 | 18 | 376 | 497 |
| User #17 | 17 | 557 | 1985 |
| User #19 | 17 | 1 | 235 |
| User #1 | 15 | 701 | 4915 |
| User #30 | 3 | 1 | 759 |
| User #60 | 3 | 1713 | 1777 |
| User #67 | 3 | 1 | 11 |
| User #45 | 2 | 302 | 475 |
| User #20 | 1 | 19 | 19 |
| User #37 | 1 | 34 | 34 |
| User #73 | 1 | 11 | 11 |

## Chat Messages

| Type | Count |
| --- | --: |
| Permanent (`1`) | 8,206 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #68 | 2991 | 94 | 52981 |
| User #5 | 1553 | 14632 | 88438 |
| User #23 | 1191 | 2340 | 44002 |
| User #7 | 798 | 19120 | 55453 |
| User #65 | 564 | 2076 | 13615 |
| User #72 | 396 | 2 | 4218 |
| User #63 | 283 | 192 | 4392 |
| User #57 | 195 | 2024 | 6379 |
| User #62 | 82 | 498 | 7763 |
| User #9 | 62 | 5630 | 6583 |
| User #12 | 48 | 3241 | 4766 |
| User #32 | 34 | 187 | 863 |
| User #1 | 5 | 3221 | 3278 |
| User #30 | 2 | 1 | 8 |
| User #67 | 2 | 1 | 11 |

## Mission Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 1,633 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #68 | 665 | 383 | 52833 |
| User #5 | 276 | 14222 | 88376 |
| User #23 | 248 | 2266 | 42903 |
| User #65 | 155 | 2075 | 13604 |
| User #7 | 111 | 19119 | 55574 |
| User #57 | 80 | 1880 | 6286 |
| User #72 | 26 | 1 | 3814 |
| User #62 | 19 | 497 | 7763 |
| User #9 | 14 | 5629 | 6317 |
| User #63 | 13 | 141 | 966 |
| User #12 | 11 | 3241 | 4235 |
| User #1 | 6 | 701 | 4915 |
| User #32 | 5 | 179 | 1148 |
| User #2 | 2 | 5062 | 5230 |
| User #17 | 1 | 557 | 557 |
| User #45 | 1 | 302 | 302 |

## Chat Contacts

| Type | Count |
| --- | --: |
| Permanent (`1`) | 79 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #22 | 32 | 411 | 629 |
| User #5 | 13 | 423 | 86668 |
| User #57 | 8 | 1326 | 6206 |
| User #0 | 6 | 16260 | 20624 |
| User #7 | 6 | 52439 | 55401 |
| User #23 | 6 | 6212 | 38508 |
| User #68 | 4 | 15466 | 34746 |
| User #9 | 2 | 5941 | 5998 |
| User #37 | 1 | 34 | 34 |
| User #62 | 1 | 6983 | 6983 |

## Inventory Items

| Type | Count |
| --- | --: |
| Permanent (`1`) | 250 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #21 | 112 | 636 | 4332 |
| User #68 | 28 | 4734 | 50993 |
| User #65 | 28 | 270 | 10604 |
| User #0 | 19 | 10513 | 24335 |
| User #19 | 13 | 1 | 121 |
| User #5 | 12 | 10529 | 65907 |
| User #72 | 10 | 108 | 180 |
| User #23 | 9 | 26042 | 38486 |
| User #57 | 7 | 168 | 1598 |
| User #1 | 4 | 824 | 4876 |
| User #62 | 3 | 1803 | 6649 |
| User #7 | 2 | 35058 | 53164 |
| User #63 | 1 | 65 | 65 |
| User #20 | 1 | 19 | 19 |
| User #73 | 1 | 11 | 11 |

## Beacon Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 147 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #2 | 97 | 12623 | 13229 |
| User #23 | 21 | 23161 | 23245 |
| User #62 | 15 | 5491 | 5581 |
| User #32 | 13 | 658 | 734 |
| User #68 | 1 | 46524 | 46524 |

## Achievement Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 60 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #17 | 16 | 565 | 1985 |
| User #2 | 15 | 2772 | 2969 |
| User #22 | 11 | 1 | 65 |
| User #72 | 10 | 108 | 180 |
| User #23 | 7 | 2349 | 41115 |
| User #63 | 1 | 65 | 65 |

## Audio Translate Show Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 16 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #21 | 11 | 2682 | 2863 |
| User #23 | 5 | 5999 | 10144 |

## Image Marker Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 138 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #2 | 118 | 11735 | 17813 |
| User #68 | 10 | 46188 | 46242 |
| User #63 | 10 | 2468 | 2530 |

## Installation Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 305 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #2 | 217 | 11353 | 18151 |
| User #23 | 23 | 8619 | 13226 |
| User #9 | 17 | 6591 | 6885 |
| User #68 | 16 | 42637 | 46242 |
| User #62 | 11 | 4305 | 7758 |
| User #63 | 10 | 2468 | 2530 |
| User #72 | 5 | 260 | 552 |
| User #60 | 3 | 1713 | 1777 |
| User #5 | 2 | 61579 | 61589 |
| User #45 | 1 | 475 | 475 |

## Map Location Data - Disneyland

| Type | Count |
| --- | --: |
| Permanent (`1`) | 363 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #2 | 190 | 13249 | 18175 |
| User #23 | 71 | 2886 | 30224 |
| User #8 | 34 | 1269 | 1539 |
| User #32 | 27 | 392 | 807 |
| User #9 | 18 | 5946 | 6936 |
| User #46 | 8 | 448 | 497 |
| User #5 | 8 | 60179 | 61605 |
| User #7 | 5 | 53205 | 55421 |
| User #68 | 1 | 46535 | 46535 |
| User #30 | 1 | 759 | 759 |

## Map Location Data - SWGS Deck 4F

| Type | Count |
| --- | --: |
| Permanent (`1`) | 25 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #72 | 11 | 292 | 1458 |
| User #62 | 11 | 5589 | 7387 |
| User #68 | 2 | 34960 | 46501 |
| User #67 | 1 | 8 | 8 |

## Map Location Data - SWGS Deck 6

| Type | Count |
| --- | --: |
| Permanent (`1`) | 16 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #62 | 10 | 5610 | 7310 |
| User #72 | 5 | 254 | 1437 |
| User #68 | 1 | 14598 | 14598 |

## Map Location Data - SWGS Deck 7

| Type | Count |
| --- | --: |
| Permanent (`1`) | 1 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #62 | 1 | 5646 | 5646 |

## Map Location Data - Walt Disney World

| Type | Count |
| --- | --: |
| Permanent (`1`) | 427 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #5 | 285 | 61611 | 68002 |
| User #23 | 71 | 5982 | 30217 |
| User #68 | 32 | 15294 | 50925 |
| User #62 | 18 | 4322 | 7221 |
| User #46 | 10 | 376 | 439 |
| User #72 | 9 | 693 | 1506 |
| User #65 | 1 | 13592 | 13592 |
| User #63 | 1 | 963 | 963 |

## Map Object Data - Disneyland

| Type | Count |
| --- | --: |
| Permanent (`1`) | 348 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #2 | 190 | 13249 | 18175 |
| User #23 | 70 | 2886 | 30224 |
| User #8 | 34 | 1269 | 1539 |
| User #32 | 26 | 420 | 807 |
| User #9 | 17 | 6873 | 6936 |
| User #46 | 8 | 448 | 497 |
| User #5 | 2 | 61599 | 61605 |
| User #68 | 1 | 46535 | 46535 |

## Map Object Data - SWGS Deck 4F

| Type | Count |
| --- | --: |
| Permanent (`1`) | 22 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #72 | 11 | 292 | 1458 |
| User #62 | 11 | 5589 | 7387 |

## Map Object Data - SWGS Deck 6

| Type | Count |
| --- | --: |
| Permanent (`1`) | 15 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #62 | 10 | 5610 | 7310 |
| User #72 | 5 | 254 | 1437 |

## Map Object Data - SWGS Deck 7

| Type | Count |
| --- | --: |
| Permanent (`1`) | 1 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #62 | 1 | 5646 | 5646 |

## Map Object Data - Walt Disney World

| Type | Count |
| --- | --: |
| Permanent (`1`) | 362 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #5 | 271 | 61611 | 68002 |
| User #23 | 70 | 5982 | 30217 |
| User #46 | 10 | 376 | 439 |
| User #72 | 7 | 1405 | 1506 |
| User #62 | 3 | 7145 | 7221 |
| User #68 | 1 | 46532 | 46532 |

## Push Notification Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 10 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #68 | 9 | 9633 | 9689 |
| User #62 | 1 | 4011 | 4011 |

## Star Map Region Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 6 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #19 | 4 | 208 | 235 |
| User #8 | 2 | 752 | 791 |

## Text Marker Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 25 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #2 | 8 | 11385 | 14860 |
| User #68 | 6 | 42637 | 42717 |
| User #23 | 4 | 8619 | 9942 |
| User #60 | 3 | 1713 | 1777 |
| User #5 | 2 | 61579 | 61589 |
| User #72 | 2 | 260 | 275 |

## Triggerable Show Data

| Type | Count |
| --- | --: |
| Permanent (`1`) | 44 |

| User | Count | Min Serial | Max Serial |
| --- | --: | --: | --: |
| User #21 | 37 | 302 | 3285 |
| User #23 | 4 | 3433 | 12903 |
| User #2 | 3 | 4335 | 4347 |

