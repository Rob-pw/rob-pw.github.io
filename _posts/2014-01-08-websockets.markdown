---
layout: post
title: "Websockets"
date: 2014-01-08 13:20:00
categories: websocket socket.io html5
---

# Introduction

The beauty of using socket.io, in comparison to standard WebSockets is that cross-browser compatability issues are reduced; socket.io automatically provides fallbacks:

-	WebSocket
-	Adobe® Flash® Socket
-	AJAX long polling
-	AJAX multipart streaming
-	Forever Iframe
-	JSONP Polling

# Server-Sent Events

Servers  and clients communicate through events, useful for splitting up types of response and binding to events.  

Servers can 'emit' events, and listen for them:
## Server
``` javascript
var io = require('socket.io').listen(8080);

//Listen for the connection event to be fired, this is done after a new successful connection is made to a client
//Provides a reference to the opened socket / client, connection specific communication done via ref.
io.sockets.on('connection', function (socket) {
	//First, we'll emit (send) a Message Of The Day to our client.
	//In this case, the event handle is 'motd' and the data is "Welcome to my server!"
	socket.emit('motd', "Welcome to my server!");

	//Now we'll add an event handler for the 'message' event, once fired it will call the function
	//An object / variable is passed, this contains all of the data sent by the client for that event. 
	socket.on('message', function(data) {
		console.log(data);
	});

	socket.on('important_message', function(data, callback) {
		callback('Roger that');
	});

	//We'll also wait for the disconnect event, this is fired when either: the client says it's disconnecting, or the client doesn't respond for a given time.
	//Connection is upheld using heartbeats, think of it as a more advanced ping system
	// Svr - "Are you still there?"
	// Cli - "Yep"
	// Svr - "Are you still there?"
	// Cli - "Yep"
	// Svr - "Are you still there?"
	// -- waits
	// Svr - "Client presumed to have disconnected, fire disconnect event."
	socket.on('disconnect', function() {
		//No data is returned, and the connection is no longer active, hence one cannot emit on socket.
		//socket.broadcast is used to broadcast to all active connections, except the socket that sent it.
		//We have passed an object with two properties: message and clientID
		//In the background, the passed object is stringified as JSON, then sent
		//At the other end, the client parses the JSON string into an object again, hence references are broken and functions cannot be communicated.
		socket.broadcast.emit('client_disconnected', {
			message : "User: " + socket.id + ", disconnected.",
			clientID : socket.id
		});
	});
});
```

## Client
``` javascript
/* 
If socket.io is running on port 80, here is the shorthand script tag
<script src="/socket.io/socket.io.js"></script>
Else, you'll have to specify the port and host.
<script src="http://localhost:8080/socket.io/socket.io.js"></script>
*/
//If port 80
//var socket = io.connect();
var socket = io.connect('http://localhost:8080');
 
//When we receive the event 'motd' from the server, run the callback passing the data
socket.on('motd', function(data) {
	//In this demo, the message "Welcome to my server!" will be alerted.
	alert("MOTD: " + data);
});
 
//Emits an event 'message', with the data "'ello friends, I am here." 
socket.emit('message', "'ello friends, I am here.");
 
//Emits an event, passes some data and calls a function once a response is received. 
socket.emit('important_message', {
	alias : "bookfort",
	message : "This is bookfort to server, do you read me? Over."
}, function(data) {
	console.log(data);
});
 
//Tells the server that we're disconnecting, raises the 'disconnect' event.
socket.disconnect(); 
```

# Rooms

Rooms are a quick and simple way of grouping clients into groups, using rooms one can easily broadcast messages to multiple sockets.

## Join Room
To put a socket (client) into a room, simply use: 
``` javascript
socket.join('exampleRoom');
```
now let's broadcast a simple message to all those in 'exampleRoom':
``` javascript
socket.broadcast.to('exampleRoom').emit('message', data);
```
or
``` javascript
io.sockets.in('exampleRoom').emit('message', data);
```

## Leave Room

Once a client disconnects, they are automatically removed from all rooms - hence 'leave' is not required. However, if one wishes to remove a client from a room, use socket.leave: 
``` javascript
socket.leave('exampleRoom');
```

## List Rooms

To retrieve a hash of all rooms, use:
``` javascript
io.sockets.manager.rooms
```
The hash key is the room name, preceeded by a '/', and the data associated is an array of socket IDs.

# Upload Files


# Response Type Support


# Basic Socket Communication


# ArrayBuffer and Blob Support

#References
- http://socket.io/#how-to-use

