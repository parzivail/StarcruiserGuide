---
layout: post
title: The Message Loop
subtitle: Android ↔ Datapad
---

# Datapad → Android

The WebView exposes a window global `Android` that contains the interface `postMessage(...)` for sending messages across the Datapad→Android boundary. It's presumably implemented on the WebView end as something like:

```java
class JsMessageBridge
{
	@JavascriptInterface
	public String postMesssage(String message)
	{ 
		// Do something with the message 
	}
}

// Later
webView.addJavascriptInterface(new JsMessageBridge(), "Android");
```

It's this interface that consumes the commands that Datapad issues. The command format is discussed below. Commands are replied to in a Android→Datapad return channel.

## Async data resolution

A typical request issued by the frontend looks something like this:

```json
{
	"reqId": 6,
	"type": "asyncResponse",
	"command": "GET_GAME_CONTENT",
	"payload": {
		"contentId": "page-counts",
		"contentVersionId": "87"
	}
}
```

Breaking it down:

* `reqId` is a key in a dictionary of promise handlers. The response message must use the same corresponding ID to make sure the response data is handled by the right promise.
* `type` is always `asyncResponse`.
* `command` is one of many command IDs that are each handled differently on the backend (see below)
* `payload` is specific to each command, and contains the parameters required by the backend handler for the command
  * In this case, the command is loading data from the Firebase dump. It's issuing a request in the format `starWarsGalaxysEdgeGame_{contentVersionId}_{contentId}.json`, which here resolves to `starWarsGalaxysEdgeGame_87_page-counts.json`.

## Events

The frontend can also subscribe to a number of events. An event subscription takes the form:

```json
{
	"reqId": 227,
	"type": "asyncResponse",
	"command": "GAME_MODE_CONFIGURATION_UPDATED",
	"payload": {
		"event_type": "EVENT_TYPE_SUBSCRIBE",
		"params": {
			"gameModeId": "swgs"
		}
	}
}
```

While these take the same basic form as async data requests, their return channel is notably different.

# Android → Datapad

## Async data resolution

The PlayAPI has a static method `handlePromiseReponse` which can either:

* Accept a serialized JSON payload message, as issued by the Android backend, in which case it's deserialized in place
* Accept a raw JavaScript object payload, as issues by an iOS backend, in which case no deserialization is necessary

This method is invoked directly by the native handlers, and there aren't any direct references to it in the frontend.

A typical message dispatched by the backend looks similar to:

```json
{
	"requestID": "6",
	"status": "SUCCESS",
	"type": "asyncResponse",
	"requestType": "GET_GAME_CONTENT",
	"payload": {
		"data": [
			{
				"lang/generated-en": 95,
				"lang/en": 12
			}
		]
	}
}
```

Breaking this one down:

* `requestID` denotes which request this data is in response to
  * The response ID is a string, while the request ID is an integer. I'm not sure why that's the case on the native side, because the frontend handles this field perfectly fine as an integer as well.
* `status` can be either `SUCCESS` for an affirmative result, or anything else for a negative.
* `type` can only be `asyncResponse`
* `requestType` mirrors the request's `command`
* `payload` is the data that has been resolved in response to the command
  * Here, it loaded the contents of `starWarsGalaxysEdgeGame_87_page-counts.json` and discovered there are 95 language file banks for the `generated-en` locale, and 12 for the `en` locale.

## Events

Event subscriptions initially respond to the subscription request with a standard data resolution message, such as:

```json
{
	"requestID": "227",
	"status": "SUCCESS",
	"type": "asyncResponse",
	"requestType": "GAME_MODE_CONFIGURATION_UPDATED",
	"payload": true
}
```

The PlayAPI has another static method `nativeCallback` which will also conditionally parse the data. This function is directly invoked when the native backend dispatches an event, and will fire the corresponding event in the frontend.

An event dispatch message takes this form:

```json
{
	"requestID": "1",
	"status": "SUCCESS",
	"type": "event",
	"requestType": "GAME_MODE_CONFIGURATION_UPDATED",
	"payload": {
		"arrived": false,
		"preArrival": false,
		"previousSailing": false,
		"isGuestOnPlanet": false,
		"duringReservation": false,
		"isGuestOnShip": false
	}
}
```

Some notes:

* Despite the event dispatching with an ID, the ID is never utilized on the frontend
* Events with `status == 'SUCCESS'` will be dispatched as a [`CustomEvent`](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) with its `type` set to the event's `requestType`, and the `detail` set to the event's `payload`
* Events with `status != 'SUCCESS'` will fire a `CustomEvent` with the `type` set to `GAME_ON_ERROR` and `detail` set to the entire event message, instead of just the `payload`

# Common Commands

| Command                                    | Type    | Description |
| ------------------------------------------ | ------- | ----------- |
| `ACCESSIBILITY_UPDATED`                    | Event   | Fired when the app's accessibility settings change. Result is stored in `Config.isAccessibilityEnabled`, and seems to add UI elements that interact with screen readers. |
| `ANALYTICS_LOG_ACTION`                     | Request | Logs button presses and UX flow, also logs some basic game state (like which jobs are available or completed), your current park, game version, etc. |
| `ANALYTICS_LOG_STATE`                      | Request | Logs game state changes (like moving into new activitiies), additionally logs the same state information as `ANALYTICS_LOG_ACTION` |
| `AR_SESSION_AVAILABLE`                     | Request | Presumably generates a successful response if ARCore is available on the device, but my device returns: `Error: ARCore is not supported` |
| `ASSETS`                                   | Request | Loads an asset from a remote source. I believe these assets are cached, and source from Firestore -- it's just used for the chat images of the Galactic Starcruiser characters, whose cast members regularly rotate |
| `ATTRACTION_DATA_QUERY`                    | Request | Queries data about the guest's interaction with a specific ride (i.e. completion dates for missions associated with the ride, etc.) |
| `ATTRACTION_DATA_UPDATED`                  | Event   | When fired, Datapad requests an `ATTRACTION_DATA_QUERY` for both the Disneyland and Disney World _Smuggler's Run_ ride |
| `BEACON_GAME_ADVANCE_IN_RANGE`             | Event   | Fired when a beacon updates. Information from the beacon is received, including the beacon name, ID, and RSSI, and the name of the experience associated with the beacon |
| `DEVICE_BLUETOOTH_STATUS_CHANGED`          | Event   | Fired when the device's Bluetooth is toggled on or off |
| `DEVICE_LOCATION_STATUS_CHANGED`           | Event   | Fired when the device's location services are toggled on or off |
| `DYNAMIC_VIBRATE_DEVICE`                   | Request | Triggers a device haptic vibration using a specified vibration pattern |
| `GAME_BACK`                                | Event   | Fired when the hardware "back" button is pressed (or gesture is activated) |
| `GAME_CLOSE`                               | Request | Informs the native layer that the Datapad is exiting |
| `GAME_INIT`                                | Request | Requests the platform initialization data from the native layer, including data on the current player and their entitlements, the player's current park location, some debugging flags, and experience toggles |
| `GAME_MODE_CONFIGURATION_UPDATED`          | Event   | Fired when the device determines there is a change in any of the following flags: whether the guest is at the park, whether the guest is at Galactic Starcruiser, and whether the guest has either an upcoming, current, or recently passed Galactic Starcruiser reservation |
| `GAME_STATE_SAVE`                          | Request | Triggers the game to save its state to disk |
| `GET_CURRENT_ENTITLEMENT`                  | Request | Returns the guest's current entitlements, if logged in. Denotes whether the user should have access to Galactic Starcruiser content |
| `GET_GAME_CONTENT`                         | Request | Request a database read for a specific table, which actually results in a specific file being loaded from disk, parsed as a Firestore database dump, and the data returned |
| `GET_GAME_MODE_CONFIGURATION`              | Request | Queries the immediate value of the current game mode, the same data as updated in `GAME_MODE_CONFIGURATION_UPDATED` |
| `LOW_POWER_MODE_CHANGED`                   | Event   | Triggered when the device's low power mode is activated or deactivated |
| `READ_PLAYER_ACHIEVEMENT`                  | Request | Queries all available achievements, returning details on each, including whether the player has been awarded each achievement |
| `SET_GAME_DATA`                            | Request | Pushes player-specific game data to a Firebase instance |
| `SHOW_CONTROL_DATA_RECEIVED`               | Event   | Fired when a beacon associated with an in-park interaction broadcasts new data |
| `SHOW_CONTROL_EFFECT_IN_RANGE`             | Event   | when a beacon associated with an in-park interaction is discovered |
| `SHOW_CONTROL_SESSION_INIT`                | Request | Initializes the native becaon subsystem |
| `TERRITORY_WAR_SCHEDULED_START_TIME`       | Event   | Fired when the next Territory War for the specified park updates the start time |
| `TERRITORY_WAR_SCHEDULED_START_TIME_QUERY` | Request | Queries the immediate value of the current park's Territory War start time |