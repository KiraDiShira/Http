- [Index](https://github.com/KiraDiShira/Http#http)

# Http security

- [The Stateful Stateless Web](#the-stateful-stateless-web)
- [Cookies](#cookies)
- [Tracing Sessions and HttpOnly](#tracing-sessions-and-httpOnly)

## The Stateful Stateless Web

HTTP is designed as a **stateless protocol** meaning each request response transaction is independent of any previous or future transaction. There's nothing in the protocol that requires a server to retain state or information about a single HTTP request. All the server needs to do is generate a response for that request and every request carry's all the information a server needs to create the response. 

While HTTP is stateless most of the applications that we build on top of HTTP are highly stateful. For example, a banking application will want to make sure that a user logs in before allowing them to view their account related resources. So every time one of these stateless requests arrives at the banking Website, the application needs to know a little bit about the user needs to know that they've already authenticated and if they haven't it needs to send them to a login page. 

Another example of a stateful application is when the user wants to open an account and they need to fill out a four step wizard. The application wants to make sure that the user completed the first step of the wizard successfully before allowing them to get to the second step. Those are going to be independent HTTP transaction but the server needs to know about the state of where the user is inside of that four step wizard. 

Fortunately there's many options for storing state in a Web application. One approach is to embed state in the resources that are being transferred to the client so that the state required by the application or at least some of that state will travel back on the next request. That approach typically requires some hidden input fields and it works the best for short lived state like tracking the state as you move through a four step wizard.

Embedding state in the resource is essentially maintaining or keeping state inside of HTTP messages and in general that's a very highly scalable approach to the Web to maintaining state but it can complicate application programming. 

Another option is to store the state on the server or behind the server and that style is required for state that has to be around a long time. So when the user submits a form to change their email address, the email address must always be associated with the user so that application can take the address, validate it and sort into a database or a file or call a Web service to let someone else take care of persisting the address. 

For server session storage many Web development frameworks like asp.net also provide access to a user session. The session may live in memory or it may live in a database but a developer can store information in the session and retrieve that information on every subsequent request from a particular user. Data stored in the session is scoped to an individual user, actually to that user's browsing session, and it's not shared among multiple users. Session storage usually has a very easy programming model and it's only good for short lived state because eventually the server has to assume that the user left the site or closed the browser and the server will discard that information. In session storage if it's kept in memory it can have some impacts on scalability because subsequent requests must go the exact same server where the session data resides. So if you're in a Web form where you have multiple Web servers, multiple machines that are actually serving the resources for one single Website, you have to make sure that the request always end up at the same machine. Some load balancers help to support that scenario by implementing what we call sticky sessions. 

I'll show you an example of session state in just a bit but you might already be wondering, how can a server track a user to implement session state? If multiple requests arrive at a server how does the server know if these requests are from the same user or two different users or multiple users? In the early days of the Web, Web server software might have differentiated users by looking at the IP address of request message. These days however, many users live behind devices using network address translation, and for that reason and various other reasons you can multiple users effectively on the same IP address and IP addresses can change. So an IP address is not a reliable technique for differentiating users. Fortunately there are more reliable techniques and they rely on cookies.

## Cookies

Websites that want to track users often turn to cookies. Cookies are defined by RFC 6265 and this RFC has the stimulating title of HTTP State Management Mechanism. This document describes how a Website can give the user's browser a cookie using an HTTP header. The browser then knows how to send that cookie and the headers of every additional request that it sends to a site.

So assuming a Website has placed some sort of unique identifying into the cookie, then the Website can now track a user as they make requests and differentiate one user from another.

Before we get into the details of what cookies look like and how they behave, it's worth noting a couple limitations. First, cookies can identify users in the sense that your cookie is different then my cookie. But cookies by themselves do not authenticate users. An authenticated user has proven their identity usually by providing credentials like a user name and password. The Cookies we're going to look at first just give us some unique identifier to differentiate one user from another and track a user as they make request to a site. 

Secondly, they do raise some privacy concerns in some circles. Some users will disable cookies in their browsers meaning the browser will reject any cookies that a server gives them. And disabled cookies present a problem for sites that need to track users of course and the alternatives are a little bit messy. For example, one approach to a cookieless session is to place some sort of user identifier into the URL, meaning each and every URL that a site gives to a user must contain the proper identifier and the URLs become much larger. That's why we often call this technique the fat URL technique. 

When a Website wants to give a user a cookie, it uses a **set cookie header** in an HTTP response. So here's an incoming request to searchengine.com, someone is searching for lyrics.

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec1.PNG" />

Searchengine.com wants to track users so in the HTTP response to that message, it's going to have a set cookie header.
There are three pieces of information in this particular cookie. The three pieces are delimited by semi colons. First there's a **collection of name value pairs** and these name value pairs themselves are delimited by a dollar sign. That's very similar to how query parameters are formatted into a URL, we looked at that in the first module. In this example the server must want to store the user's first name and last name in the cookie. The second and third pieces of information are the **domain** and the **path**, we'll circle back around and talk about those a little bit later. Now a Website can put any information that it wants into a cookie but many Websites will only put a unique identifier, perhaps a Guid. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec2.PNG" />

And there's a couple reasons for this. One is, there is a size limitation in cookies of around four kilobytes and secondly, a server can never really trust anything that it stores on the client unless it's cryptographically secured. So while it is possible to store encrypted data in a cookie, it's usually just easier to store an ID. Assuming the browser is configured to accept cookies then the browser will take that cookie and it's going to send it along in any subsequent request it that it makes to searchengine.com that GUID will be there.

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec3.PNG" />

And when the ID arrives at the server, the server can use that to look up the associated data for that user from an in memory data structure or from a database or from a distributed cache. You can configure most Web application frameworks to manipulate cookies automatically and look up session state for you. Let's take a look at an example of how this works.

## Tracing Sessions and HttpOnly

One other piece that I want to point out is that if I go to a different browser and this time I'll go to Internet Explorer and if we try to go to the same page, signedup.cshtml, it doesn't know my name. And this is because cookies get set in a browser and yes they are per user but if the user is using different browsers or has cookies disabled that can sort of mess things up. 

So first of all I want to point out that first name and last name, that was not data that was stored in the Cookie. Instead the only thing stored in the cookie is some sort of session identifier. First name and last name are stored by default with asp.net in memory on the Web server. The Web server's just using this cookie value to look up the proper data structure in memory. Secondly, we might look at this ID, u3ylzcntnrr, etc. and wonder why it's so complicated. Well one security concern around session identifiers is how they can open up the possibility of someone high jacking some other user's session. 

I want to point out the **HTTPOnly** flag here because another security concern around cookies is that they are vulnerable to a **cross site scripting attack**. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec4.PNG" />

In a cross site scripting attack a malicious user injects Java script code into someone else's Website and if the other Website sends that malicious script to their users, a script has the ability to modify and inspect and steal cookie information. So a malicious script could find my asp.net session ID and perhaps use an Ajax request to send it off to some other server where someone's recording these things and then they know my session ID. To stop that sort of problem it was actually Microsoft that introduced this HTTPOnly flag and it's now a standard. And what the HTTPOnly flag tells the browser, the user agent, is that it should not allow script code to access this cookie. This cookie exists only to put into HTTP request and travel in the header of every HTTP request message. So browsers that correctly implement HTTPOnly, and most of them do these days, will not allow clients like Javascript to read or write this cookie on the client. And that is a very good thing because cross site scripting attacks are very popular these days.
