---
published: true
layout: post
subtitle: Speaking to D-Beacons in their natural habitat
title: StarWardriving
---
# Background

In a [previous post](/swge-inspector/), I discussed the details of a BLE datalogging platform, dubbed the "SWGE Inspector". During a recent visit to Galaxy's Edge, I was able to test out the initial hardware setup. In this post we'll discuss the methodology, the data, some possible interpretations, and look into some potential areas of improvement.

# Setup 

## Hardware

The sensor platform was modified to include an nRF51822-based BLE packet sniffer [from Adafruit](https://www.adafruit.com/product/2269). This prevented the original Bluetooth dongle from performing double-duty and instead solely as a connection point to [the monitoring app, Stardust](https://github.com/parzivail/SwgeStardust). It also had the advantage of producing a pcap-compatible file with useful metadata like BLE packet RSSI, channel, and status flags. Unfortunately, the sniffer had no attachment point for an external antenna, so the chip antenna's performance had to suffice. The platform was otherwise as described.

Additionally, an Android device running a patched version of Play Disney Parks v2.21.1 was used to log app communication both between the Datapad native and web layer and between the Datapad and beacon system. The logging patch was adapted from the one described in [this post](breaking-the-loop/). This Android device also ran Stardust.

## Software

The SWGE Inspector's Pi 4 ran a copy of [SwgeInspector](https://github.com/parzivail/SwgeInspector) whose primary function was to ingest data from the sensor suite and log them unmodified to a file with timestamps, and to serve basic statistics to Stardust.

On a remote desktop, a copy of [SwgeDatapadEmulator](https://github.com/parzivail/SwgeDatapadEmulator) hosted a WebSocket server for the patched Datapad to communicate with. It timestamped and saved all socket activity generated by the logging patch.

After the data had been collected, the data was processed by the Unpacker project in SwgeInspector.

# Data Processing

All told, the 3-hour jaunt at Galaxy's Edge generated about 100 megabytes of raw data.

| Source | Size |
| :-- | --: |
| BLE Packets | 8.4 MiB |
| GPS NMEA Sentences | 10.5 MiB |
| IMU Data | 37.2 MiB |
| Datapad Logs | 38.5 MiB |
| **Total** | **94.6 MiB** |

## Time synchronizing

The Pi 4 has neither an RTC nor backup battery, so as long as power is disconnected, the system clock is frozen in time. This is typically remediated by connecting to an NTP server at boot, but the airgapped Pi remained unconnected throughout the trip. Luckily, the constant stream of GPS-synchronized UTC timestamps from the GPS allows the offset between the system time and UTC to be determined. Clock drift on the Pi 4 has been measured by others online to be [up to 12 seconds per day in some conditions](https://forums.raspberrypi.com/viewtopic.php?t=211140) without an internet connection. While this could be corrected for using the UTC timestamps throughout the trip, I didn't correct for it — a second or two over the three hours at the park shouldn't skew the results too much.

## GPS

All of the incoming packets are timestamped, but I also wanted them to be position-stamped so they can be plotted on a map. When the GPS has a fix, it will emit coordinates at 1 Hz, but more often than not, BLE packets and Datapad log packets are received by the system at some instant between two coordinate fixes. The easiest solution was to commit GIS heresy and linearly interpolate between the two GPS fixes nearest in time at the instant the packets are received.

I also filtered out fixes that had fewer than 10 satellites providing the fix. Only 4 are required for a basic fix, but 10 was arbitrarily chosen as a good compromise between keeping many points while discarding low-quality indoor points.

Datapad internally does not work directly with GPS fixes and instead uses the beacons for positioning (as described in [the D-Beacon post](/dbeacon/)). This means that beacon locations aren't natively given geographic coordinates, and instead are given 2D plane coordinates local to the Datapad map image of your park (the map that's displayed in the map tab of Datapad). Every set of plane coordinates is given an ID, which is referenced elsewhere when the location is needed:

```json-doc
// Excerpt of map-location-data-wdw.json
"d6T9eMCYs1kCcFx5Esbb": {
    "id": "72057615512826119",
    "x": 0.14887291667593763,
    "y": 0.49989224539017885
}
```

Beacons are mapped to locations in yet another file:

```json-doc
// Excerpt of map-object-data-wdw.json
"4bVkyPJvdWG7ud5fLUQz": {
    "beaconId": "72057602627875400",
    "id": "72057615512826119",
    "locationId": "72057615512826119",
    "name": "blp-02e4b-04",
    "priority": "HIGH",
    "type": "BeaconMarker"
}
```

At Hollywood Studios, the map image looks like the following with beacons plotted (resized 25%, click to enlarge):

[![The map of Galaxy's Edge as seen from within the Datapad app, with beacons drawn on top](/images/packet_mapping/map-wdw-beacons-small.jpg)](/images/packet_mapping/map-wdw-beacons.jpg)

A mapping between Datapad's 2D plane coordinates was manually created by overlaying the Datapad map on a map with known physical boundaries, and calculating the offset. The process was eyeballed, and the maps don't quite overlap (the Datapad map is somewhat stylized), but nothing should be off by more than a handful of feet.

## BLE

The BLE packets I'm interested in are likely in this format, the beacon format discovered by some other brilliant folks to [emulate droid "reaction" location beacons](https://github.com/ruthsarian/pxt-swge-beacon):

![Droid location beacon format](/images/packet_mapping/location_beacon_example.png)

In Wireshark, we can display only packets that have a valid CRC (as computed by the sniffer) and are advertising with a Company ID of `0x0183`, which is the Company ID assigned to Walt Disney by the Bluetooth SIG. The resulting filter would be:

```
(nordic_ble.crcok == 1) && (btcommon.eir_ad.entry.company_id == 0x0183)
```

Of the 140,313 packets captured at the park, this narrows our investigation down to 6,094, or about 4%. Following the packet format we're interested in, we expect the manufacturer-specific data to be 6 bytes long, so tacking an extra `(len(btcommon.eir_ad.entry.data) == 6)` onto the filter brings us down to 1,461 packets. Here's a breakdown of the packets by device type:

| Device Type | Identifier | Count |
| --- | --- | --- |
| Location | `0x0A` | 114 |
| Droid | `0x03` | 181 |
| ??? | `0x10` | 1,166 |

A few packets were captured with the Location and Droid beacon types, but the majority of packets are of a new unknown type, `0x10`. Across the packets, only two bytes seem to be variable:

![Droid location beacon format](/images/packet_mapping/unknown_beacon_example.png)

On a hunch, since the byte at offset `0x4` varied the most, I figured it must be related to the location of the beacon. Inspecting the resources in Datapad, `beacon-data.json` looks promising: it contains entries mapping app installations to "waypoint IDs", which seem to be small integers.

```json-doc
// Excerpt of beacon-data.json
"CDqjl4fc0gqjsKxsS3op": {
    "id": "72057602627875400",
    "installationsUnlocked": [
        "72057602627878469"
    ],
    "name": "blp-02e4b-04",
    "playExperienceNameDLR": "dlr-dl-swge-blp-02e4b-04",
    "playExperienceNameWDW": "wdw-dhs-swge-blp-02e4b-04",
    "type": "Sensor",
    "waypointId": 61
}
```

Plotting both the packets and the beacons on a map near the Droid Depot and connecting packets to beacons using the byte at offset `0x4` as the waypoint ID yields the following:

* Blue points indicate packets
* Red points indicate beacons, their names, and locations as defined by Datapad
* Cyan points indicate orphan points which don't correlate to any known waypoint ID
  * Since orphans appear in clumps, I assume a few new beacons are transmitting waypoint IDs that haven't made their way into production as of Datapad v2.21.1

![A political map of Galaxy's Edge plotting beacon and packet locations](/images/packet_mapping/osm_droidbath_beacons.png)

That's progress! Here are two more maps, a detail shot of the marketplace and one of the entire park.

![A political map of Galaxy's Edge plotting beacon and packet locations](/images/packet_mapping/osm_market_beacons.png)

(Overview map resized 25%, click to enlarge)
[![A political map of Galaxy's Edge plotting beacon and packet locations](/images/packet_mapping/osm_map_beacons_small.jpg)](/images/packet_mapping/osm_map_beacons.jpg)

That leaves one byte unknown at offset `0x5`. Only 3 values were observed: `0xA1`, `0xBA`, and `0xBF`. Following the standard set by the droid-related beacons (minimum RSSI as a signed byte), we would get minimum RSSIs of `-95`, `-70`, and `-65`, respectively. I assume that would indicate the minimum RSSI required to incorporate that beacon into beacon-based navigation or to interact with the installation. All of the values are reasonable, but the exact meaning is still unknown.

I compiled a table of the physical addresses of the Bluetooth radios that theoretically map to each beacon, but the table is rather long, so it's in Appendix 1.

# Going Forward

## Hardware

The number of packets captured by the sniffer was disappointing for three hours of deliberately covering and re-tracing as much area on foot as possible. Adding an external antenna to the Adafruit sniffer is impossible, but another nRF51822-based device that incorporates an external antenna could be reflashed to use the sniffer firmware. Relocating the device outside of a backpack, or reducing the number of obstructions between the receiving radio and beacon (for example, the Pelican case and other PCBs), would also likely improve the received packet count. I was impressed, however, that some of the beacons were received so far away — one possible explanation is that the beacon radio is transmitting at a relatively high power, or another is that the radio spectrum was in the perfect condition at that moment to be heard over the noise at great length.

The Pi 4 was computational overkill for the required task. Sipping 4W while active, I got about 10 to 15 hours of battery life while at the park. The Pi also dumped 4W of heat into the case, theoretically reducing the SNR of the Bluetooth radios. Although the Pi was set to throttle the CPU at 50 degrees C, the heat dissipation and power consumption could be dramatically reduced by moving the task to a microcontroller. Since the task boiled down to ingesting serial streams and dumping them to an SD card, most microcontrollers with multiple high-performance serial lanes would work. Luckily, all of the radio and IMU boards used in the Inspector have versions without the USB-to-serial converter and expose a serial connection directly.

The clock drift of the Pi 4 wasn't measured, but if a longer trip to the park is planned, it'd be a good idea to install a proper RTC module into whatever device is timestamping the incoming data.

## Software

The Wireshark filter that produced these packets left over 4,000 packets advertising the Walt Disney company ID unaccounted for. Since they don't seem to follow the format of a typical beacon advertisement, it's possible that they're show control messages to and from installations. These would be an interesting payload to dissect in the future.

Although the IMU data wasn't utilized this time, it could be used in the future to augment the GPS data, both to improve location accuracy indoors and to prevent the need to filter out all GPS fixes under a certain satellite count.

I plan to use the beacon information to revise the SWGE Inspector for another trip to the park, next time using a local copy of the beacon locations to estimate position, emulating the Datapad functionality.

# Appendix 1: Beacon MAC/Physical Address Table

Among the 79 physical addresses received that advertised the Walt Disney company ID and the Beacon magic header, 77 different waypoints were found. 8 waypoints were orphans as of v2.21.1.

| Beacon | Likely Physical Address |
| --- | --- |
| blp-0001-07   | E8:55:BE:58:BB:F4 |
| blp-01101-01  | F6:09:0F:DB:EE:D5 |
| blp-01101-02  | D9:7D:11:5D:24:79 |
| blp-01110-01  | E8:43:95:07:77:9F |
| blp-01119-02  | E3:B6:A0:51:43:F2 |
| blp-01136-01  | E1:65:C4:41:A1:A8 |
| blp-01e1b-01  | DB:8D:AD:B3:A6:67 |
| blp-01e1b-02  | E5:B8:CD:53:57:17 |
| blp-01e2a-02  | C8:69:20:3D:AB:34 |
| blp-01e2a-03  | D9:A5:DF:02:E2:A7 |
| blp-01e4a-02  | F5:E8:59:BD:4B:F6 |
| blp-01e4a-03  | FE:8E:6F:90:79:FD |
| blp-01e4a-04  | D4:99:B5:58:A7:39 |
| blp-01e5a-01  | EE:A3:35:35:8F:70 |
| blp-01e5a-02  | FA:08:A7:D2:06:C4 |
| blp-01e5a-04  | D9:2B:38:EC:E9:3E |
| blp-01e5a-05  | E6:9A:29:E7:5F:CD |
| blp-01e5a-06  | EA:5D:52:A2:F2:45 |
| blp-02112-01  | DF:C6:87:67:D1:97 |
| blp-02112-02  | D5:DB:38:0E:49:E1 |
| blp-02e1a-02  | E9:7E:A8:78:D0:07 |
| blp-02e1a-03  | D1:C7:68:87:77:4E |
| blp-02e1a-04  | E1:3B:8C:A1:51:B9 |
| blp-02e1b-01  | F3:F3:A5:96:C4:53 |
| blp-02e2a-02  | F7:9F:CF:D0:98:95 |
| blp-02e2b-01  | C1:5E:51:BE:7C:36, CE:05:11:52:CB:A5 |
| blp-02e2b-02  | CE:69:8E:C4:80:60 |
| blp-02e3a-01  | D9:34:79:A9:82:E5 |
| blp-02e3a-02  | D0:78:2F:ED:2D:80 |
| blp-02e3a-04  | D4:A1:D8:CA:F5:2B |
| blp-02e4b-01  | E4:10:3C:98:2C:69 |
| blp-02e4b-04  | E8:7A:FB:11:15:0F |
| blp-02e4b-05  | C9:1C:3A:7B:BF:49 |
| blp-02e4b-06  | CF:F9:55:3C:EF:F6 |
| blp-02e4b-07  | C1:D6:94:F9:CE:08 |
| blp-06e1a-01  | D9:DE:66:E1:54:D4 |
| blp-06e1b-01  | E9:7E:61:95:DD:EF |
| blp-06e1b-02  | EC:33:52:66:97:8C |
| blp-06e1b-03  | EA:40:12:E0:7B:7D |
| blp-06e2b-01  | F7:BA:8F:34:11:7E |
| blp-06e2b-02  | E2:5B:86:C1:0C:E2 |
| blp-07e1a-02  | CF:C4:72:67:1C:86 |
| blp-07e1b-01  | C6:6D:E6:14:1C:E7 |
| blp-07e1c-01  | EC:52:F1:27:A7:81 |
| bss-0001-01   | FE:8A:78:72:57:1A |
| bss-0001-02   | CD:61:5E:9D:6E:AC |
| bss-0001-03   | DE:B2:1E:DA:29:7B |
| bss-0001-05   | DE:B7:33:83:F5:77 |
| bss-0001-09   | E1:3D:7C:F9:C3:2F |
| bss-0001-10   | CC:17:DB:3E:C2:8A |
| bss-0001-11   | CE:F5:DE:44:B9:9E |
| bss-0001-12   | D0:AF:D7:39:D4:BC |
| bss-0004-02   | C4:13:92:67:C6:3D |
| bss-0004-03   | DC:BD:19:92:2F:9E |
| bss-0004-04   | DD:49:80:10:C7:A0 |
| bss-0007-01   | E0:63:3D:77:14:C0 |
| bss-0007-02   | C7:C5:A2:0A:A6:17 |
| bss-00b01     | E6:C3:97:3B:61:FC |
| bss-01110-01  | EA:01:20:13:C9:1E |
| bss-01119-01  | DD:2B:A8:DD:BE:9C |
| bss-01136-01  | EA:B2:DA:DF:0C:F2 |
| bss-01e4a-01  | EA:2C:0A:E7:4E:76 |
| bss-02112-01  | CC:6E:0D:6D:70:1E |
| bss-02130-01  | E1:9F:78:C0:BE:03, E7:AF:75:FD:45:0E |
| bss-02e3a-01  | F5:A0:92:B2:4C:0E |
| bss-02e3a-02  | FD:F3:2E:C2:7F:2B |
| bss-0302      | DE:2F:C3:C8:CA:6E |
| bss-07e1b-01  | CD:0E:15:AB:55:26 |
| bss-1801      | EA:F6:17:6C:8D:7B |
| <orphan 0x46> | C8:E7:6A:0F:E6:3A |
| <orphan 0x6A> | C4:82:3E:4A:60:D3 |
| <orphan 0x75> | E0:2F:FB:F4:38:34 |
| <orphan 0x77> | F1:6D:32:D6:42:AD |
| <orphan 0x79> | E3:2C:EF:4B:5D:A1 |
| <orphan 0x81> | D3:47:15:2D:03:FC |
| <orphan 0x8E> | FE:CD:1E:40:1D:8B |
| <orphan 0xB2> | EF:F9:47:BB:EA:AD |
