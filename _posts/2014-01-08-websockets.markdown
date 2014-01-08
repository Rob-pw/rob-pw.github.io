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

## Server-Sent Events

Servers  and clients communicate through events, useful for splitting up types of response and binding to events.  

Servers can 'emit' events, and listen for them:
### Server
{% gist 8317292 %}

### Client
{% gist 8317268 %}

## Upload Files


## Response Type Support


## Basic Socket Communication


## ArrayBuffer and Blob Support



