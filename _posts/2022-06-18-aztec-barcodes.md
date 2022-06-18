---
published: true
layout: post
title: Aztec Barcodes
subtitle: What's Datapad actually scanning?
---
It turns out that the Mexico Pavilion at Epcot's World Showcase isn't the only place you can find a Mesoamerican pyramid lookalike in the parks! Datapad's scanning functionality scans a form of matrix barcode called an [Aztec code](https://en.wikipedia.org/wiki/Aztec_Code). These codes are similar to other matrix barcodes like the [QR code](https://en.wikipedia.org/wiki/QR_code), but their geometry uses a singular tracking symbol in the middle (which resembles an Aztec pyramid when viewed from above), encoding data outwards in rings, instead of using multiple tracking symbols and encoding data in the interior space.

# Park usage

In addition to being theming elements throughout the park, many of the crates are adorned with a small plaque that contains a hexagonal pattern, used as a static image marker to differentiate barcode usage types, and a central Aztec code. Scanning them in Datapad grants the guest an inventory item, typically after a "manifest decryption" minigame. Story elements are also distributed by cast members to Galactic Starcruiser guests at areas like Oga's Cantina — these can be scanned to advance through specific areas of the Starcruiser storyline. 

| Crate barcode plaque | Story element barcode |
| :-: | :-: |
| ![An Aztec code on a crate](/images/aztec/swge_plaque.jpg) | ![An Aztec code on a coaster](/images/aztec/swgs_story_coaster.jpg) |
| Encodes `KL_ST`, ECC 58% | Encodes `JK_RS`, ECC 58% |

# Encoded data

Each Aztec matrix encodes 4 or 5 characters of data, in the formats `XX_XX`, `FALNN` and `CASTNN`, where `X` is any uppercase letter and `N` is any digit. These marker IDs are unique to a tag but multiple IDs can point to the same data. Installation data is stored locally within Datapad, a database is included that links between marker IDs and installation IDs. A separate [local document store](/installation-data/) defines data on each installation, linking to their [rewards](/item-data/).