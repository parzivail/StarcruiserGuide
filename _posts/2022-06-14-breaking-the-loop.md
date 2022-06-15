---
published: true
layout: post
subtitle: WebSockets for message loop fun and profit
title: Breaking the Loop
---
# Overview

We can take advantage of the fact that the WebView implementation supports (secure) WebSockets to intercept the messages traveling through the [message loop](/message-loop/). In doing so, we can:

* Log the messages passing across that threshold
* Instrument a man-in-the-middle attack to inject arbitrary responses into the datastream
* Fully replace the Android backend with a custom one

The first point is fully implemented with the [SwgeDatapadEmulator project](https://github.com/parzivail/SwgeDatapadEmulator) which also is attempting the third, but more data is required from actually _within_ the park to do anything much more useful than create arbitrary SWGS agendas and queue up character conversations.

Patching the frontend is as simple as adding some JavaScript to Datapad's `index.html` either directly or by appending it to a script that's already loaded. Repacking the app will reflect your changes. 

# Secure WebSockets

For one reason or another, the WebView used here only allows secure WebSocket connections. To make a successful connection, you'll need a domain name and to generate an SSL certificate for that domain — [Let's Encrypt](https://letsencrypt.org/) works well for this. If you're using something like [WebSocketSharp](https://github.com/sta/websocket-sharp) in C#, you can use your `fullchain.pem` and `privkey.pem` to generate an X.509 certificate like so:

```csharp
var cert = X509Certificate2.CreateFromPemFile("fullchain.pem", "privkey.pem");

var wss = new WebSocketServer(ADAPTER_IP, PORT, true)
{
	SslConfiguration =
	{
		ServerCertificate = new X509Certificate2(cert.Export(X509ContentType.Pkcs12))
	}
};
```

# Backend Emulation

The message loop ingest starts at the PlayAPI method `handlePromiseReponse`, which deserializes and bubbles the command message through to the relevant subsystem. We can inject our own messages by passing all incoming socket data straight through that method. We can exfiltrate outgoing messages by overwriting the bridge method with a stub that forwards the message over the socket.

```javascript
const _interopSocket = new WebSocket('wss://YOUR_DOMAIN_NAME.com');

_interopSocket.addEventListener('message', function (message) {
	PlayAPI.handlePromiseReponse(message.data)
});

window.Android = {
	postMessage: function(message) {
		_interopSocket.send(message);
	}
};
```

# Loop Logging

Ironically, simply logging the data that passes in either direction across the boundary is more involved. It's largely complicated by the fact that overwriting the methods in question will obviously destroy the original functionality of those methods, so we'll need to alias the methods instead. We can capture a few different types of events, including errors, and send them down the socket along with a timestamp and optional payload.

```javascript
const _interopSocket = new WebSocket('wss://YOUR_DOMAIN_NAME.com');
var _isConnected = false;

_interopSocket.addEventListener('open', function (event) {
	_isConnected = true;
	_interopSocket.send(JSON.stringify({
		type: "CONNECT",
		timestamp: new Date().toISOString()
	}))
});

window.addEventListener('error', (event) => {
	if (_isConnected)
		_interopSocket.send(JSON.stringify({
			type: "WINDOW_ERROR",
			timestamp: new Date().toISOString(),
			payload: event
		}))
});

try {
	var original_sendCommand = _PlayAPI._sendCommand;

	_PlayAPI._sendCommand = function(cmdObject) {
		if (_isConnected)
			_interopSocket.send(JSON.stringify({
				type: "COMMAND_JS_TO_NATIVE",
				timestamp: new Date().toISOString(),
				payload: cmdObject
			}))
	
		original_sendCommand.call(_PlayAPI, cmdObject);
	};
	
	var original_handlePromiseReponse = _PlayAPI._handlePromiseReponse;
	
	_PlayAPI._handlePromiseReponse = function(res) {
		var response = _PlayAPI._parseIfNecessary(res);
		if (_isConnected)
			_interopSocket.send(JSON.stringify({
				type: "COMMAND_NATIVE_TO_JS",
				timestamp: new Date().toISOString(),
				payload: response
			}))
	
		original_handlePromiseReponse.call(_PlayAPI, res);
	};
	
	var original_nativeCallback = PlayAPI.nativeCallback;
	
	PlayAPI.nativeCallback = function(res) {
		var response = _PlayAPI._parseIfNecessary(res);
		if (_isConnected)
			_interopSocket.send(JSON.stringify({
				type: "COMMAND_NATIVE_CALLBACK",
				timestamp: new Date().toISOString(),
				payload: response
			}))
	
		original_nativeCallback.call(PlayAPI, res);
	}
}
catch (err) {
	if (_isConnected)
			_interopSocket.send(JSON.stringify({
			type: "INIT_ERROR",
			timestamp: new Date().toISOString(),
			payload: err
		}))
}
```
