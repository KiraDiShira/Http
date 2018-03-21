- [Index](https://github.com/KiraDiShira/Http#http)

# Http messages

- [Message Types](#message-types)
- [HTTP Methods](#http-methods)
- [Safe Methods](#safe-methods)
- [Request Messages](#request-messages)

## Message Types

HTTP is a request and response protocol, so the first type of message is the **HTTP request**. That's what the client sends to the server, and they carefully format that message so that the server will understand it. A server responds by using a different type of message, the second type of message, which is an **HTTP response**. And again, that message will be formatted so that the client will understand. 

It's formatted according to the HTTP/1.1 specification. The request and the response are two different message types, but they get exchanged inside of a single HTTP transaction.

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPMessages/Images/httpmess1.PNG" />

The standards define what goes into those messages, so that everyone who speaks HTTP will understand each other and be able to exchange resources. Or, when the resource doesn't exist, the HTTP response can contain an error message, that the client will understand that that resource didn't exist.

## HTTP Methods

The method tells the server what the request wants to do. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPMessages/Images/httpmess2.PNG" />

## Safe Methods

There is a part of the HTTP specification that talks about safe HTTP methods. Safe methods are methods that you read and view resources from a Web server. Unsafe methods are methods that let you change resources on a Web server. 

## Request Messages

A full request message will consist of the following parts: There's the method; and then the URL; and finally, the version. This message is always in ASCII text, by the way, and this first line is what we call the start line. The HTTP version that you'll see in the start line is typically going to be 1.1, because that's the standard that's been around since 1999. 

After the start line, there can be one or more headers, and then there can be a body to the HTTP message. For get requests, you typically don't see a body. You just see the start line and then one or more headers. And I keep saying one or more, because, remember, the host header is a required header (The reason for the 'Host' header is to make explicit which host this request refers to. Without 'Host', the server must know ahead of time that it is supposed to route 'http://joesdogs.com/' to Joe's Dogs while it is supposed to route 'http://joscats.com/' to Jo's Cats even though they are on the same webserver. (What if a server has 2 names, like 'joscats.com' and 'joescats.com' that should refer to the same website?)Having an explicit 'Host' header make these kinds of decisions much easier to program.). 

Headers generally contain useful information that can help a server process a request. For example, in Part 1, we talked about resource representations and how the client and server can negotiate on the best representation of a resource. That's what we called content negotiation. If the client wants to see a resource in French, for example, this is where it will include a header entry that says the language I want to accept is French. Then there are numerous other headers that are defined by the HTTP specifications. Some of the headers are general headers that can appear in a request or a response. One example of that is the date header that you see here. And that allows the client or the server to include a header that indicates when it created this message. Now, everything but the host header is optional. But when a header does appear, it has to obey the standards. So the HTTP specification points to another standard that describes the standard format for dates. Here are some of the other popular request headers that you might see on an outgoing request. One is the Referer header -- and yes, it's misspelled in the standard. The Referer will contain the URL of the referring page, so when the user clicks on a link, the client can send the URL of the page that referred the user to the resource that it's getting. With the Web browsers, there's typically also a user agent header that describes what user agent is making this request. On the server, you can parse that user agent and figure out if the user is using Internet Explorer 6 or Chrome or Firefox. There's an Accept header that describes the media types that the user agent will accept, and the Accept Language that describes the preferred language. You'll also see Cookie headers in an HTTP request. We'll talk about cookies in a later module. And then, If-Modified-Since is one of these headers that you'll commonly see. The If-Modified-Since header tells the server when the user agent last retrieved this resource. And so, if the resource hasn't changed, the server does not have to send the entire resource back to the client. It can just tell it, yes, you have the most recent version. If-Modified-Since is quite commonly used to retrieve images, because the browser can aggressively cache images and improve performance, by not retrieving an entire image, if it already has a copy in the local file system. So when you put all these together, a full HTTP request message might look like this. We're going to get the root resource, using HTTP/1.1. We'll have a Host header, because that's required. We want the connection to stay alive. We'll talk about that in the next module. Here's the user agent header that says this is essentially Chrome, version 16. Chrome will happily accept HTML text, also XHTML. At the very end of that Accept header we're basically saying, give me anything that you have and I'll try to figure out what it is. There is a Refer, so we landed on this page as the result of a Google search. And then there's some more Accept headers that describe the preferred language, the preferred character set, and the encoding that Chrome understands. So since it understands gzip, the server can compress certain resources, to send them down to the client. Notice that some of these headers can contain multiple values, so the Accept header says text/HTML comma, some other mime type comma, some other mime type. And you'll also notice this Q value. The Q value is always a number from zero to one, and it represents the relative quality value, or what the specification calls the relative degree of preference. So something with a Q value of 0.9 is just slightly more preferred than something with a Q value of 0.8, but far more preferred than something that has a Q value of 0.3.
