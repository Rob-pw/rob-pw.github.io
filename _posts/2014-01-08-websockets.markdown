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
{% gist 8317292 %}

## Client
{% gist 8317268 %}

# Rooms

Rooms are a quick and simple way of grouping clients into groups, using rooms one can easily broadcast messages to multiple sockets.

## Join Room
To put a socket (client) into a room, simply use: 
{% highlight javascript %}
socket.join('exampleRoom');
{% endhighlight %}
now let's broadcast a simple message to all those in 'exampleRoom':
{% highlight javascript %}
socket.broadcast.to('exampleRoom').emit('message', data);
{% endhighlight %}
or
{% highlight javascript %}
io.sockets.in('exampleRoom').emit('message', data);
{% endhighlight %}

## Leave Room

Once a client disconnects, they are automatically removed from all rooms - hence 'leave' is not required. However, if one wishes to remove a client from a room, use socket.leave: 
{% highlight javascript %}
socket.leave('exampleRoom');
{% endhighlight %}

## List Rooms

To retrieve a hash of all rooms, use:
{% highlight javascript %}
io.sockets.manager.rooms
{% endhighlight %}
The hash key is the room name, preceeded by a '/', and the data associated is an array of socket IDs.

# Upload Files


# Response Type Support


# Basic Socket Communication


# ArrayBuffer and Blob Support

#References
- http://socket.io/#how-to-use

