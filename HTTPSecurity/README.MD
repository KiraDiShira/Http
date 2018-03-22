- [Index](https://github.com/KiraDiShira/Http#http)

# Http security

- [The Stateful Stateless Web](#the-stateful-stateless-web)
- [Cookies](#cookies)
- [Tracing Sessions and HttpOnly](#tracing-sessions-and-httpOnly)
- [Cookie Paths, Domains, and Persistence](#cookie-paths-domains-and-persistence)
- [Basic and Digest Authentication](#basic-and-digest-authentication)
- [Windows Authentication](#windows-authentication)

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

## Cookie Paths, Domains, and Persistence

So far all the cookies we've looked at are what we would call **session cookies**. Don't confuse that with the session object or session data on the server. It's a specific type of cookie that we call a session cookie because it exists for only a single user session. It's get destroyed when the user closes their browser. So in this example we've gone to searchengine.com and it used a set cookie header to give the browser a cookie with a GUID value inside of it and every subsequent request that the browser makes to searchengine.com it's going to pass along that GUID value until the user closes their browser and then the browser simply forgets about that cookie.

A **persistent cookie** is the other type of cookie and it can outlive a single browsing session because the browser, the user agent, will typically store that cookie to the file system to disc. So I can shut down a computer and come back one week later, go to my favorite Website and a persistent cookie would still be around for the first request. 

The only difference between the two is that a persistent cookie needs an expires value. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec5.PNG" />

So what we're looking at right here, I know it's a session cookie because there is no expires value in the cookie. However, this cookie is a cookie that has an expires value. This cookie is going to be around until July 9, 2012. 

The next piece that I want to talk about is this **domain value**. I've said that once a cookie is set by a Website, the cookie travels to that Website with every request. However, not all cookies travel to every Website. The only cookies a user agent should send to a site are the cookies that the site gave it. It wouldn't make sense for cookies from Amazon.com to be an HTTP request to Google.com. That type of behavior would only open up additional security and privacy concerns and Google.com really shouldn't understand what's inside of Amazon.com's cookies anyway. So if you set a cookie in a response to www.searchengine.com the resulting cookie should only travel in requests to www.searchengine.com. A Web application can change that a little bit and restrict the cookie to a specific host or domain or even to a specific resource path by using this domain and this **path attribute**.

The domain attribute basically allows a cookie to span sub-domains. In other words, if you set a cookie from www.searchengine.com the browser's only going to deliver that cookie to www.searchengine.com. But if I set a cookie and I say that the domain is `.searchengine.com` that allows the cookie to travel to any URL in the searchengine.com domain. That would include images dot searchengine.com and help dot searchenginel.com. So you cannot use this domain attribute to span domains, in other words, if the browser makes a request to searchengine.com and it tries to set a cookie with a domain set to Microsoft.com that's not legal, the user agent should reject the cookie. But if I go to www.searchengine.com it will be allowed to set the cookie domain to dot searchengine.com which is essentially telling the browser don't just send this to the www server, send it to anything that ends with dot searchengine.com. 

The path attribute, that's another way to restrict a cookie to a specific resource path. So in this example the cookie will travel to basically anything under dot searchengine.com but if we sent that path to something like slash stuff or slash images, that would be telling the browser only send this cookie to something on searchengine.com when the URL path starts with slash stuff or slash images. Path settings can help you to organize cookies when there's multiple teams building Web applications in different paths.

## Basic and Digest Authentication

Cookies are good for tracking and differentiating one user from another user but sometimes we need to know an individual user's identity. We need to know exactly who they are. A process of authentication forces a user to prove their identity by entering a user name and a password or an email and a pin or some other type of credentials.

With the Web, authentication follows a challenge response format. A client will request a secure resource from the server and the server will challenge the client to authenticate by sending back an HTTP response with a challenge inside of it. The client then needs to send another request and include authentication credentials for the server to validate. If the credentials are good that request will succeed. 

The extendability of HTTP allows HTTP to support various different authentication protocols. In this module I'm going to briefly look at the top five which include, **basic authentication, digest, Windows, forms and open ID**. Of these five, only two are official in the HTTP specification, the basic and digest authentication protocols and we'll first talk about basic authentication. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec6.PNG" />

With **basic authentication** the client requests a resource with a normal HTTP message and the Web server, most of them will let you configure access to specific files and directories. You can allow access to all anonymous users or restrict access to the only specific users or groups can access a particular file or directory. For this request, imagine the server's configured to only allow users that have authenticated themselves to view a slash account resource. In this case the server then has taken that anonymous request and returned a challenge saying I need to authenticate, the authentication protocol is the basic authentication protocol and notice the 401 status code, that is telling the client the request is unauthorized. A `www-authenticate` header tells the client to collect the user credentials and then try this again. The `basic realm` attribute, that gives the user agent a description of the protected area.

And what happens next depends on the specific browser but most browsers will open up a dialogue that allows the user to enter their user name and password. But once that happens the browser can send another request to the server and this request will include an authorization header. And the value of the authorization header is the client's user name and password and with basic authentication the user name and password is just base 64 encoded. That means basic authentication is insecure because anyone who can view that message can find out the user's name and password. So for that reason basic authentication is rarely used without secure HTTP which we'll look at later. 

But at this point it's up to the server to decode the authorization header, verify the user name and password by checking with the operating system or checking against something that's in a database or whatever credential management system is configured on the server. If the credentials match the server can make a reply and say yes, here's the account resource. If the credentials don't match, the server should respond with a 401 status, you are still unauthorized to view this. 

**Digest authentication** is another authentication protocol that's included as part of the HTTP specification and it is an improvement over basic authentication because it does not transmit user passwords using base 64 encoding. 

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec7.PNG" />

Instead the client sends a digest of the password and the client needs to compute this digest using an Md5 hashing algorithm with a nonce that the server provides during the authentication challenge that helps to prevent replay attacks (Nell'ambito della sicurezza informatica il replay-attack è una forma di attacco di rete che consiste nell'impossessarsi di una credenziale di autenticazione comunicata da un host ad un altro, e riproporla successivamente simulando l'identità dell'emittente.). So this is very similar to basic authentication, there's still a www dash authenticate header that the server will send back, it just includes some additional information that the client will need to use in his calculations so they have some cryptographic value. 

And then the client will also send back another request with an authorize header that now includes an encrypted form of the user name and password. And the server again can validate those and let the request through or reject the credentials and say this is still an unauthorized request. So digest authentication is better then basic authentication when secure HTTP is not available but it's still far from perfect because digest authentication is still vulnerable to man in the middle attacks. That's where someone can install a malicious proxy server that's looking at HTTP messages as the flow across the network and it sees what your authorization token is using digest authentication. Someone can still steal that piece of information and use it to access the server.

## Windows Authentication

Windows authentication is very popular when you have Microsoft servers and Microsoft products. Although it is supported by many modern browsers, not just Internet Explorer, it's just that it does require Windows machine as you Web server and it doesn't work so well over the internet or where proxy servers might reside. So it's commonly used for internal and internet Websites particularly at companies that have Microsoft active directories set up and they're using active directory to manage their users and groups and permissions.

Windows authentication behaves very much like basic and digest authentication in the sense that a client makes a request for a resource that has been secured so the server will challenge that request with an HTTP 401 status code reply saying that was unauthorized, please authenticate.

<img src="https://github.com/KiraDiShira/Http/blob/master/HTTPSecurity/Images/Sec8.PNG" />

And in this case the value of the dub dub dub dash authenticate header will be negotiate. That's a key word, the client will interpret to mean Windows authentication specifically we can negotiate on the protocol because Windows supports a couple different security providers. There's NTLM and there's Kerberos. We can pick which one and agree on it and the next request I'll send along some credential information that will be encrypted. You can decipher that and figure out if the credentials are good or not and allow me in with the next request. And that still comes up in an authorize header. So because things are encrypted Windows authentication has the advantage of being a little more secure even without using secure HTTP and in some cases it can even be unobtrusive. 

So let's go into IAS and what I'm going to do is disable basic authentication. And enable Windows authentication and I'll also point out that I would have digest authentication available here as an option if I had it installed in IAS. But it does require an active directory server to be available and I don't have that available so it's not an option here. Windows authentication ironically is available without active directory server. What we'll be authenticating against is just the users that are on this machine. So with that in place let's open up Chrome and try to get to this page again. And now I'll be prompted to enter a user name and password and Chrome sort of knows what credentials I'm using here on the Windows machine. I could go ahead and try those or I could enter in the credentials for a different user and that allows me to get to the page. Now I can also come over into Internet Explorer and we go to search dot cshtml and I'm just instantly taken there but that's because in Internet Explorer one of the options that you can set here, if we go into custom level for security, is that it will automatically log me in with my current user name and password for sites that are in a specific zone and that means it's using the same credentials that I used to log in to this machine to access the server which in this case is what I wanted. And that usually works really well in an active directory setting.
