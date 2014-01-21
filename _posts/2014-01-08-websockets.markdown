# WebSockets, an overview: Javascript | HTML5 | Node.js | ws. 

## Summary
WebSockets are extremely useful for persistent real-time data communication, they're event driven, low overhead, and highly asyncronous which allows for lightweight non-blocking transport of information.

## Browser Support
WebSockets, without Socket.io: 
	Browser	Supported From
	Internet Explorer	10.0
	Firefox 	6.0
	Chrome	14.0
	Safari	6.0
	Opera	12.1

/*WebSockets, with Socket.io : 
	Browser	Supported From
	Internet Explorer	5.5
	Firefox		3.0
	Chrome	4.0
	Safari	3.0
	Opera	10.61*/

## Introduction
WebSockets are one of the newest, and most impressive contenders of the HTML5 Spec; capable of dishing out JSON/Binary in duplex, maintaining a persistent connection to the client, suffering a mere 2-14 bytes[0] of overhead per message, operating in real-time, it's asyncronous (non-blocking) and also event-driven. 

While support for pure WebSockets is good, it's recommended that you use a wrapper library such as Socket.io / SockJS / Primus, as this provides any required fallback (polyfill) mechanisms; in the case of Socket.io this allows you to target browsers as old as I.E. 5.5! 

Some such fallback options are:

-	Adobe® Flash® Socket
-	AJAX long polling
-	AJAX multipart streaming
-	Forever Iframe
-	JSONP Polling

### Practical usage
Since WebSockets allow for persistent bidirectional data transferal, they have the potential to serve many purposes.  

#### Chatroom
One such example is a chatroom, where data is sent from the client, to the server, filtered, then redistributed back out to the clients.

#### Staggered page loading
Pages can be loaded in chunks and rendered as they're received, this rapidly decreases time spent waiting for the page to initially appear and instantly provides feedback to the user. 

#### Auto-saving
Whilst the user is working on the application, the WebSocket can be streaming out the user data to be stored remotely.

Without further ado, let's cast our eye upon some examples.

## Getting Started with WebSockets
### Client
*First*, let's create a WebSocket instance
``` javascript
var socket = new WebSocket('ws://localhost:8081');
```
*The "ws" protocol is insecure, similar to HTTP; if you're transferring personal data then you will need to research how to implement wss. *

This will automatically attempt establishment of a connection to the server running on localhost on port 8081.

There are a number of events that can be attached to:

- open; once the connection is established.
- message; when a message is received.
- error; if an error is encountered.
- close; once the connection is closed.
- ping; if a ping request is received.
- pong; if a pong request is received.

Let's bind to the 'onopen' event so that we can perform actions once connected.

``` javascript
socket.onopen = function() {
	socket.send("Hello, Server!");
};
```
The function is called once the 'onopen' event is fired (immediately after a successful connection is made).

We then send the string "Hello, Server!" on the opened connection, *this is a message*.

Sending objects down the socket is simple! If instead of a string we wanted to pass the following object: 
``` javascript
var me = {
	name : "Robert White",
	alias : "Rob PW",
	website : "rob.pw",
	age : 18,
	email : "me@rob.pw"
};
```
Then we simply stringify it using JSON.
``` javascript
JSON.stringify(me);
```

And now we can send it! 
``` javascript
socket.send(JSON.stringify(me));
```

What if the server sends us some info? Well, we can simply listen for it using onmessage.
``` javascript
socket.onmessage = function(event) {
	console.log(event.data || "The server didn't say anything - spooky.");
};
```

And if we get an error? 
``` javascript
socket.onerror = function(error) {
	console.error("A fatal connnection error was encountered:", error);
};
```

Finally, let's handle when the server closes the connection.
``` javascript
socket.onclose = function() {
	console.log("Server closed the connection.\n Goodbye.");
};
```

#### All together now
``` javascript
var socket = new WebSocket('ws://localhost:8081')
    , print = function(toPrint, level) {
      /* Simple function which both logs, and writes onto the page */
      console[level || "log"](toPrint);
      document.getElementsByTagName('pre')[0].innerText += toPrint + "\n";
    };

var me = {
    name : "Robert White",
    alias : "Rob PW",
    website : "rob.pw",
    age : 18,
    email : "me@rob.pw"
};

socket.onopen = function() {
    // As soon as we connect, let's say hi.
    socket.send("Hello, server!");
    // You must stringify your objects before sending
    socket.send(JSON.stringify(me));
    // Which will send: {"name":"Robert White","alias":"Rob PW","website":"rob.pw","age":18,"email":"me@rob.pw"}
};

socket.onmessage = function(e) {
    print(e.data || "The server didn't say anything - spooky.");
};

socket.onerror = function(error) {
    print(error, "error");
};

socket.onclose = function(code, data) {
    print("Goodbye!\n You have now disconnected.");
};
```

### Node.js Server

Meanwhile, there's some simple code on the server to handle our sockets. 

Create a new WebSocket server, using the ws library which adheres to the WebSocket protocol. We'll have it running on port 8081. 
``` javascript
var WebSocketServer = require('ws').Server
,	wsServ = new WebSocketServer({
		port : 8081
	});
```

Now let's listen for when someone establishes a connection.

``` javascript
wsServ.on('connection', function(socket) {
	// In here we'll put our code which handles the connected client.
});
```
The API is largely the same, so let's send some data.
``` javascript
// As soon as they connect, let's say hi.
socket.send('Hello, client!');

// We can send data as JSON, but remember to turn it into a string first.
socket.send(JSON.stringify({
    "abc" : 1,
    "dorame" : 2
}));
```

Ẁe'll also listen out for when they send a message, then just echo it out.
``` javascript
socket.on('message', function(message) {
   console.log("Client says: " + message);
});
```

And if the connection is closed?:
``` javascript
socket.on('close', function() {
   console.log("Client has gone :c");
});
```

Finally, just for demo purposes, let's disconnect them after 5 seconds to simulate booting a client from the server.
``` javascript
setTimeout(function() {
    try {
        socket.send("Server forced disconnect.");
        socket.close();
    } catch(ex) {
        console.error(ex.message);
    }
}, 5000);
```

#### All together now
``` javascript
"use strict";

//setup Dependencies
var WebSocketServer = require('ws').Server
    ,   port = (process.env.port || 8081)
    ,   wsServ = new WebSocketServer({port : port});

wsServ.on('connection', function(socket) {
    // As soon as they connect, let's say hi.
    socket.send('Hello, client!');

    // We can send data as JSON, but remember to turn it into a string first.
    socket.send(JSON.stringify({
        "abc" : 1,
        "dorame" : 2
    }));

    socket.on('message', function(message) {
       console.log("Client says: " + message);
    });

    socket.on('close', function() {
       console.log("Client has gone :c");
    });

    setTimeout(function() {
        try {
            socket.send("Server forced disconnect.");
            socket.close();
        } catch(ex) {
            console.error(ex.message);
        }
    }, 5000);
});

console.log('Listening on ' + port );
```

### Output
#### Client
``` javascript
Hello, client!
{"abc":1,"dorame":2}
Server forced disconnect.
Goodbye!
 You have now disconnected.
```

#### Server
``` javascript
Listening on 8081
Client says: Hello, server!
Client says: {"name":"Robert White","alias":"Rob PW","website":"rob.pw","age":18,"email":"me@rob.pw"}
Client has gone :c
```

## Recap
In summary, WebSockets are a simple modern technique for bidirectional communication, they have small overheads and are remarkably fast. 

### On the client you initialise WebSockets using 
``` javascript
var socket = new WebSocket('ws://localhost:port');
```

### Have the following events to handle:
``` javascript
- open; once the connection is established.
- message; when a message is received.
- error; if an error is encountered.
- close; once the connection is closed.
- ping; if a ping request is received.
- pong; if a pong request is received.
```

### Communicate via:
``` javascript
socket.send(data);
```
where data is a string.

### To turn an object into a string, and back again, we can use:
``` javascript
// Creates a string
JSON.stringify(object);

// String to object
JSON.parse(string);
```

And that's pretty much all that there is to it! :) 

## Project usage
// Todo

## Github: https://github.com/Rob-pw/rob-pw.github.io/blob/master/_posts/2014-01-08-websockets.markdown

## Reference Links
0. http://stackoverflow.com/questions/8902731/websockets-useful-for-reducing-overhead

## Internal related articles
// Todo

<!--
# Upload Files
ch

# Response Type Support


# Basic Socket Communication


# ArrayBuffer and Blob Support
-->