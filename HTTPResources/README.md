# Http resources

- [Uniform Resource Locators](#uniform-resource-locators)
- [Ports, Queries, and Fragments](#ports-queries-and-fragments)

## Uniform Resource Locators

`http://www.food.com`

It's what we call a URL, **Uniform Resource Locator**. Resources are things I want to interact with on the web: images, pages, files and videos.

<img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res1.PNG' />

**url schema**: how to access to a particular resource (http, https, ftp, mailto). Everything after `://` will be specific to a particular scheme. So, a legal HTTP URL may not be a legal mail to URL:

```
ftp://server.com/download/tpsreport.pdf
mailto://scott@odetocode.com
```

**host**: which computer on the internet is hosting the resource. My computer will use the **domain name system** to look up an address for `food.com`, turn that into a network address and then it will know exactly where to send a request. You can also specify that host portion using the IP address directly but most people want to use a friendly name like `food.com` instead of `204.78.50.82`.

**url path**: The `food.com` host should recognize what specific resources requested by this path and respond appropriately. A path looks very hierarchical like a file system path and sometimes a URL will point to a real resource that is on the host's file system or hard drive. For example, the URL `food.com/logo.jpeg` might point to a jpeg file that really does exist on the `food.co`m server; however, resources can also be dynamic. The URL `food.com/recipes/broccoli probably` doesn't refer to a real file on the `food.com` server. Instead, some sort of application is running on the `food.com` host that will take that request and build a resource using content from a database. 

Some resources will also lead the browser to download additional resources. This `food.com` page will include images, JAVA script files, cascading style sheets and other resources that all combine together to present the recipe that we're viewing.

## Ports, Queries, and Fragments

<img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res2.PNG' />

 Number 80 represents the port number that the host is going to use to listen for HTTP requests. So you only need to specify the port number if the server is listening when a port other than the default port, port 80, and that usually only happens in testing, debugging and development environments because most commercial websites don't want a port number in their URL, it just makes the URL that much harder to remember and it makes the URL a little bit longer.
 
 <img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res3.PNG' />
 
 <img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res4.PNG' />
 
 The fragment is different than the other pieces we've looked at so far because unlike the path in the query string the fragment is not processed by the server. The fragment is only used on the client and it identifies a particular section of a resource that the client should navigate to or focus on. Web browsers will typical align the initial display of a web page such that the element identified in the fragment will be at the top of the screen.
 
  <img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res5.PNG' />
 
 ## URL Encoding
 
The official standards describing URLs go to great lengths to make sure URLs are as useable and interoperable as possible. A URL should be as easy to communicate through email as it is to put on a billboard or a bumper sticker or a business card. For this reason, the standards to find unsafe characters for URLs and unsafe character is a character that should not appear in an URL. For example, the space character is considered unsafe because spaces are hard to read. They can mistakenly appear/disappear when a URL is in printed form. Other unsafe characters include the pound sign because it's used to delimit a fragment. That doesn't mean that you cannot use a pound sign in a URL; it just means that the pound sign can only be used in its reserved position which is to delimit a fragment. Another unsafe character is the caret because it isn't always transmitted correctly through the network. RFC 3986, which is the Internet standard or the law for URLs, defines the following safe characters:
 
 <img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res6.PNG' />
 
Unfortunately, you can still transmit unsafe characters in a URL, but they need to be percent encoded or URL encoded; two different terms but it's the same outcome. Percent encoding is the process of taking a character like the space character and a URL and replacing it with a percent 20; 20 is the hexadecimal value for the space character in the US ASCII character set and so a percent encoding is basically taking that hexadecimal value, putting a percent in front and then replacing a character in the URL with that percent encoded value.

 <img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res7.PNG' />

As an example, if you really did want to have `Scott Allen` in a URL, it would need to be encoded so that it would come out to be Scott%20Allen. Almost every web application framework will make this really easy. They all have APIs for URL encoding. On the service side, you should run dynamically created URLs through the encoding algorithm just in case one of the unsafe characters will appear in the URL.
 
## Content Types

There are thousands of different resources on the web. There are images, there are hypertext documents, xml documents, video files, audio files, executable applications, PDF documents and Word documents. In order for a host to properly serve a resource and in order for the client to properly display a resource, the parties involved have to be very specific and precise about the type of resource.

So when a host responds to an HTTP request, it returns a resource and also specifies the **content type**. This is also known as the **media type** of the resource. 

The content type that a server will specify rely on the **Multi-purpose Internet Mail Extensions** or **MIME** standards. Although MIME was originally designed for email communications it worked so well that HTTP uses these standards for the same purpose, which is to label the content in a way that the client will know what the content is.

 <img src='https://github.com/KiraDiShira/Http/blob/master/HTTPResources/Images/res8.PNG' />
