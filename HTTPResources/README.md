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
 
 
