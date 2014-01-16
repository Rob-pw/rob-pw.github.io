# WebSockets, an overview: Javascript | HTML5. 

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
WebSockets are one of the newest, and most impressive contenders of the HTML5 Spec; capable of dishing out JSON/Binary in duplex, a mere 2-14 bytes[0] of overhead per message, operating in real-time, it's asyncronous (non-blocking) and also event-driven. While support for pure WebSockets is good, it's recommended that you use a wrapper library such as Socket.io / SockJS / Primus, as this provides any required fallback (polyfill) mechanisms; in the case of Socket.io this allows you to target browsers as old as I.E. 5.5! 

Some such fallback options are:
-	Adobe® Flash® Socket
-	AJAX long polling
-	AJAX multipart streaming
-	Forever Iframe
-	JSONP Polling

Without further ado, let's cast our eye upon some examples.

## Getting Started with WebSockets

``` javascript

```


# Upload Files
ch

# Response Type Support


# Basic Socket Communication


# ArrayBuffer and Blob Support

#References
- http://socket.io/#how-to-use

