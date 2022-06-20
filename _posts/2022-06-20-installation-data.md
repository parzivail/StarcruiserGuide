---
published: true
layout: post
title: Installation Data
subtitle: How beacon interactions are defined
---
From scanning and translating to hacking and tuning, _installations_ are points within the park where a guest can interact with show element through Datapad. Datapad defines six types of installations, each with their own unique features or functions, in addition to two meta-installations.

# Installation Types

| Type | ID | Description |
| --- | --- | --- |
| Device | `DeviceInstallation` | Used primarily for door access panels, but also used for moisture vaporators, antennas, maps, Zabaka's workshop window, the Falcon, etc. |
| Droid | `DroidInstallation` | Droids like R-3X in Oga's Cantina, L4-R6 and C2-B9 outside of the droid workshop, etc. |
| Tune | `TuneInstallation` | Typically wall- and turret-mounted antennas throughout the park, but also the Deck 6 atrium console at SWGS |
| Scan | `ScanInstallation` | [Crate barcodes](/aztec-barcodes/) that grant rewards |
| Text Translate | `TextTranslateInstallation` | Areas where Aurebesh is written in a place that can be scanned and translated |
| Audio Translate | `AudioTranslateInstallation` | Only used in Dok-Ondar's den, to translate a sample of Ithorese |
| Scan Group (Meta-installation) | `ScanGroupInstallation` | Groups Scan installations into similar or area-based groups, used for specific activities that only require interacting with one crate from each group |
| Mid-Level Group (Meta-installation) | `MidLevelGroupInstallation` | Groups physically localized installations together to group them at far zoom levels on the map |

## Models

These structures aren't installations themselves but are used in installation definitions.

### reward

| Field | Type | Description |
| --- | --- | --- |
| `quantity` | int | Quantity of the entity to give |
| `type` | string | Inventory item type, one of: [`Item`](/item-data/), `Credits` |
| `itemId` | string | [Datapad Snowflake](/datapad-snowflake/) that represents the inventory item, if type is `Item` |

### image

| Field | Type | Description |
| --- | --- | --- |
| `imagePath` | string | Image path relative to the content root |

## Generic Installations

These fields are common to all installation types.

Notes:

* If no thumbnail image is defined, a generic one will be used

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | [Datapad Snowflake](/datapad-snowflake/) that represents this entity |
| `type` | string | Installation type ID, as defined above |
| `name` | string | Human-readable internal name of the installation |
| `nameKey` | string | Localization lookup key for the name |
| `nameKeyAlt` | string | Alternate localization lookup key for the name which overrides `nameKey`, if present |
| `descriptionKey` | string | Localization lookup key for the description |
| `canUseWaypointOnly` | string | Allows enabling the installation in Datapad if a relevant [beacon](/dbeacon/) packet is received even if the corresponding wayside beacon is unavailable |
| `doesAutoStartSwgsChat` | bool | If true, immediately starts the mission defined by `triggerableMission` when interacted with |
| `triggerableMission` | bool | Mission to immediately start on interaction |
| `doesShowOnDisambig` | bool | If true, shows on the \"Multiple items detected\" screen as an interactive entity |
| `doesShowOnMap` | bool | If true, shows on the map as an interactive location |
| `firstTimeRewards` | reward[] | Reward to grant to the guest on first scan |
| `subsequentRewards` | reward[] | Reward to grant to the guest on first scan |
| `images` | image[] | Thumbnail images used in the detail page. If more than one is specified, the first is used. If none are specified, falls back to the relevant thumbnail image |
| `isBeaconLocked` | bool | If true, the installation is locked in Datapad unless the installation beacon is physically nearby |
| `triggerableShows` | string[] | A list of [Datapad Snowflakes](/datapad-snowflake/) that represent the [triggerable shows](/triggerable-show-data/) this installation activates |
| `dlr_detailViewKeyAltText` | string | Localization lookup key for the thumbnail alt-text at DLR |
| `dlr_thumbnailAltText` | string | Human-readable internal thumbnail alt-text at DLR |
| `dlr_thumbnailPath` | string | Image path relative to the content root for the DLR thumbnail |
| `wdw_detailViewKeyAltText` | string | Localization lookup key for the name |
| `wdw_thumbnailAltText` | string | Human-readable internal thumbnail alt-text at WDW |
| `wdw_thumbnailPath` | string | Image path relative to the content root for the WDW thumbnail |
| `premiumRewards` | ?[] | Unused, not parsed out of the JSON |
| `showInCastUI` | bool | Unused, not parsed out of the JSON |

## Device Installation

| Field | Type | Description |
| --- | --- | --- |
| `isBlip` | bool | True if the show effect is resident to the locking beacon and not a separate show effect |
| `isTerritoryWarEnabled` | bool | True if the installation should appear in Territory War filtered results when a Territory War is active |

## Droid Installation

No installation-specific fields.

## Tune Installation

| Field | Type | Description |
| --- | --- | --- |
| `transmissions` | string[] | List of [Datapad Snowflakes](/datapad-snowflake/) that represent the Transmission [inventory items](/item-data/) this installation grants |

## Scan Installation

Notes:

* If the total rewards granted by the installation on any scan total to zero, a No Access dialog will be shown

| Field | Type | Description |
| --- | --- | --- |
| `barcodeData` | string | The data encoded in the corresponding [Aztec barcode](/aztec-barcodes/) |
| `isEncrypted` | bool | True if the installation should require the completion of a decryption minigame before awarding the contents |
| `isSwgsScan` | bool | True to show a Not Available dialog instead of the No Access dialog when the Datapad is in SWGS mode and no corresponding installation is currently available |

## Text Translate Installation

| Field | Type | Description |
| --- | --- | --- |
| `markerPaths` | string[] | List of image paths, relative to the content root, to use as the AR recognition markers |

## Audio Translate Installation

| Field | Type | Description |
| --- | --- | --- |
| `audioTranslateShows` | string[] | Ordered list of [Datapad Snowflakes](/datapad-snowflake/) that represent the [audio translate shows](/item-data/) this installation can trigger in sequence |

## Scan Group Installation

| Field | Type | Description |
| --- | --- | --- |
| `installationIds` | string[] | List of [Datapad Snowflakes](/datapad-snowflake/) that represent the installations that the group contains |

## Mid-Level Group Installation

| Field | Type | Description |
| --- | --- | --- |
| `installationIds` | string[] | List of [Datapad Snowflakes](/datapad-snowflake/) that represent the installations that the group contains |
| `canSplitWhenZoomedIn` | bool | True if the map icon can split up at closer zoom levels into the constituent installations |
