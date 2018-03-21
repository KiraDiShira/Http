- [Index](https://github.com/KiraDiShira/Http#http)

# Http messages

- [Message Types](#message-types)
- [HTTP Methods](#http-methods)

## Message Types

HTTP is a request and response protocol, so the first type of message is the **HTTP request**. That's what the client sends to the server, and they carefully format that message so that the server will understand it. A server responds by using a different type of message, the second type of message, which is an **HTTP response**. And again, that message will be formatted so that the client will understand. 

It's formatted according to the HTTP/1.1 specification. The request and the response are two different message types, but they get exchanged inside of a single HTTP transaction.

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPMessages/Images/httpmess1.PNG" />

The standards define what goes into those messages, so that everyone who speaks HTTP will understand each other and be able to exchange resources. Or, when the resource doesn't exist, the HTTP response can contain an error message, that the client will understand that that resource didn't exist.

## HTTP Methods

The method tells the server what the request wants to do. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPMessages/Images/httpmess2.PNG" />
