- [Index](https://github.com/KiraDiShira/Http#http)

# Http architecture

- [Proxies](#proxies)

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
