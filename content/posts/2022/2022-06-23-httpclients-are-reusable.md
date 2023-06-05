---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2022-06-23T19:46:04+00:00'
guid: https://nuculabs.dev/?p=2224
id: 2224
layout: post
permalink: /2022/06/23/httpclients-are-reusable/
tags:
- http
- http client
- Java
title: HTTPClients are reusable
---
Hi 👋,


I wanted to write this article to tell you that instantiating a **HttpClient** class is often a costly operation.


If you have a method that does something like:


```
public class Main {
  static void myMethod() {
    HttpClient client = new HttpClient()
    // do stuff
  }
}
```


You should refactor it ASAP. Either move the HttpClient into the class and perhaps ensure that the class is a **Singleton** or retrieve the HttpClient instance using an **Object Pool** pattern like the HttpClient class from [System.Net.Http](https://docs.microsoft.com/en-us/dotnet/api/system.net.http?view=net-6.0).


The [HttpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=net-6.0) class instance acts as a session to send HTTP requests. An [HttpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=net-6.0) instance is a collection of settings applied to all requests executed by that instance. In addition, every [HttpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=net-6.0) instance uses its own connection pool, isolating its requests from requests executed by other [HttpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=net-6.0) instances.


Thanks for reading!


## Further reading


- 