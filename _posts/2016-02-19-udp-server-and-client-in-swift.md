---
layout: post
title: UDP Server and Client in Swift
permalink: /2016/02/udp-server-and-client-in-swift/
---
I recently had a chance to work on a iOS Client written in Swift that would talk to a UDP server. The app basically needed to send a request to the server and receive the response.

All over UDP. It turned out to be quite a simple task if done using the [CocoaAsyncSocket](https://github.com/robbiehanson/CocoaAsyncSocket).
I set up a simple Mac OS X test server and a corresponding iOS test client. The sources are here:

*   [https://github.com/stansidel/udpclient_swift](https://github.com/stansidel/udpclient_swift)
*   [https://github.com/stansidel/udpserver_swift](https://github.com/stansidel/udpserver_swift)

My worries about evil routers blocking responses due to NATs turned out to be false.
Most routers now support [NAT traversal](https://en.wikipedia.org/wiki/NAT_traversal).
The only exception I've found so far is Microsoft ISA working as a router.
It cannot do the traversal thus blocking the responses.

It's also worth mentioning the [UDP hole punching technique](https://en.wikipedia.org/wiki/UDP_hole_punching).
I haven't used it by now, but would probably turn to that when more real-world usage data comes in.