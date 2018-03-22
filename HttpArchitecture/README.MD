- [Index](https://github.com/KiraDiShira/Http#http)

# Http architecture

- [Proxies](#proxies)
- [Proxy Services](#proxy-services)
- [Caching](#caching)

## Proxies

A proxy server is a server that sits between a client and a server. A proxy is mostly transparent to the end user. So you think you're sending a request directly to a server, but the HTTP request message is actually going to the proxy, which will take that message and forward it to the server that you want it to get to. It can also, then, wait on the response from the server and forward it back to the client. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch1.png" />

But before forwarding either of those messages, the proxy can also inspect the message and potentially take some initial actions. For example, one of the clients I work for uses a proxy to capture all HTTP traffic leaving the office. They don't want employees and contractors spending their time on Twitter and Facebook, so HTTP requests to those servers will never reach their destinations, and there's no tweeting or Farmville inside the office. That's an example of one popular role for a proxy server, which is to function as an access control device. 

But a proxy server can actually be much more sophisticated than just dropping messages that are trying to get to specific hosts. Any firewall could do that. A proxy server can also inspect messages, to remove confidential data, like strip out the Referer header from HTTP messages, if that Referer points to an internal resource inside the company network. An access control proxy can also log all the HTTP messages, create audit trails on traffic. And many of these access control proxies require a user to log in, before they can access the Web. That's a topic we'll look at in the next module. 

The proxy I'm describing here is what we would categorize as a **forward proxy**. A forward proxy is usually closer on the network to the client than it is to a server, usually one or two network hops away from any particular client. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch2.png" />

And forward proxies usually require some configuration in the client software or Web browser to work. The idea is that the forward proxy is providing some services, to benefit just the users in a particular location, not the Internet as a whole; so the users at a specific company or in a specific office building, or the customers of a single Internet service provider. 

Another category of proxy servers is the **reverse proxy**. A reverse proxy is a proxy server that's usually closer to a server than it is to the client, and these are usually completely transparent to the client.

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch3.png" />

A reverse proxy exists to provide some benefit to a specific website, and it can indirectly benefit all the users on the Internet, because all the requests coming to servers for that website are coming through the reverse proxy. Now, both these types of proxies -- forward proxies and reverse proxies -- they can provide a wide range of services. For example, if we return to that gzip compression scenario we talked about earlier, a proxy server has the capability to compress response message bodies. A company might use a proxy server for compression, to take some of the load off of the server where the application actually lives. Now, neither the application or the Web server software itself has to worry about compression. Instead, it's a feature that's layered in, via a proxy server. That's the beauty of HTTP.

## Proxy Services

Proxies can perform a wide range of services.

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch4.png" />

For example, **load balancing**. This is where a proxy takes incoming messages and distributes them to one of several Web servers on a round-robin basis, or by knowing which server is currently processing the fewest number of requests (or CPU usage or on content types based, For example, a company might put all their images and static assets on a server optimized for serving those types of assets.)

There's also proxy servers that implement **SSL acceleration**. This is where the proxy server actually does the encryption and decryption of HTTP messages, taking that load off of the Web servers. 

Proxies can also add an additional **layer of security**, by filtering out potentially dangerous HTTP messages. Specifically, some proxies can look for messages that might have cross-site scripting attacks or SQL injection attacks embedded inside of them. 

And finally, **caching proxies** will store copies of frequently-accessed resources and respond to messages requesting those resources directly. That typically improves performance. 

## Caching 

We talked about proxy servers possibly caching information. Caching is an optimization, to improve performance and scalability. When there are multiple requests for the same resource representation, a server can send the bytes over the network time and time again, for each request. Or a proxy server or a client can cache the representation locally and reduce the amount of time and bandwidth required for a full retrieval. Caching can help reduce latency, help prevent bottlenecks, and allow a Web application to survive, when every user shows up at once to buy the newest product or see the latest press release. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch5.png" />

Caching is also a great example of how the metadata in an HTTP message facilitates additional layers and services. 

The first thing to know is that there are two types of caches. A **public cache** is a cache shared among multiple users. A public cache generally resides on a proxy server.

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch6.png" />

A public cache on a forward proxy is usually caching the resources that are popular in a community of users, like the users from a specific company, or the uses of a specific Internet service provider. A public cache on a reverse proxy is generally caching the resources that are popular on a specific website, like popular product images from Amazon.com. Those are public caches. 

A **private cache** is dedicated to a single user. Web browsers always keep a private cache of resources on your disk. These are the temporary Internet files in Internet Explorer, or type about:cache in the address bar of Google Chrome, to see the files in its private cache. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch7.png" />

Anything a browser has cached on the file system can appear almost instantly on the screen. The browser doesn't even have to send off a request. 

The rules about what to cache, when to cache, and when to invalidate the cache -- that is, kick an item out of the cache, because it's no longer fresh or up-to-date -- they are a little bit complicated and mired by some legacy headers and behaviors. But allow me to point out some of the things that you should know. 

First of all, with HTTP 1.1, clients and proxies generally want to cache a response that has a 200 okay status code and that is the response to an HTTP get request. Remember, we talked about safe and unsafe methods in an earlier module, and get is a safe method. It's not supposed to change state on the server, and we can send off as many get requests as we like, without messing up the application. Put, post, and delete are considered unsafe, because we use them to change state on the server. We use a post request to submit a credit card transaction, change a profile, log in to a site. Most everyone will avoid caching these types of requests, because bad things can happen. 

Now, an application or server can influence the cache settings by using the proper HTTP headers and a response. In HTTP 1.1, this header is the **cache control header**, although you can also see an expires header in many messages. The **expires header** is still around and widely supported, despite being deprecated in HTTP 1.1. **Pragma** is another example of a header commonly used to control caching behavior, but it, too, is really only around for backward compatibility. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HttpArchitecture/Images/arch8.png" />

So we are going to focus in on cache control.



An HTTP response can have a value for cache control of public, private, or no cache. A value of public means public proxy servers can cache the response. This response is for anyone. A value of private means the response is really targeted to a single user. So only private caches should keep those; that is, caches in the Web browser. And of course, no cache is telling everyone in the world that they shouldn't cache this response. There's also a no store value, meaning the message might contain some sensitive information and it shouldn't be persisted at all. It should be removed from memory as soon as possible. Now that you know this, how would you use this type of information? Well, for popular requests for shared resources, like a homepage logo, you might want to use a public cache control directive, to allow everyone to cache the image, even proxy servers. For requests that are going to a specific user, like the HTML for the home page and that HTML include to the user's name, you want to use a private cache directive. You don't want other users to have the wrong user name. In ASP.NET, you can control these settings via response.cache. So there's response.cache.setcachability. Set it to public or private or no cache. And there's also an expiration that you can set, because once an item goes into the cache, it may not want to live there forever. You might just want to cache something for 10 seconds, because information changes every 10 seconds. Or you might want to cache it for 10 years, because it's an image and you don't expect it to change. Here is an HTTP response for an image, and you can see it's using a cache control of private, and the expiration is set by the max age value. Max age is specified in seconds. This particular cache control setting is saying that this is good for at least 31 million seconds. That would be about 10 years. And notice there's another header here, Last-Modified. The browser can use that piece of information as a validator. That is, how can I validate that this thing I have in the cache is still good. Well, it could go to the server and request that image again and say, Hey, here's the last date that I have for the image. Has it changed since then? And if it has changed, the server can send the new image. If it hasn't changed, the server can send a special response that we looked at earlier, the 304 response that says this content wasn't modified. What you have in the cache is good. Let's actually go out and use Fiddler and see some of these requests and response headers.
