---
author: "Denis Nu\u021Biu"
categories:
- Web Development
date: '2018-05-22T18:49:36+00:00'
guid: /2018/05/22/angular-http-interceptors-what-are-they-and-how-to-use-them/
id: 26
layout: post
permalink: /2018/05/22/angular-http-interceptors-what-are-they-and-how-to-use-them/
tags:
- Angular
- Framework
- Front End Development
- Https
- JavaScript
title: 'Angular HTTP Interceptors: What are they and how to use them'
---
![](/wp-content/uploads/2018/06/5fd91-1kagsp7vmmipbmj5k5stkha.jpeg)
Photo by [Tyler Milligan](https://unsplash.com/photos/dSIcpkddKrM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/ruby?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
Hello,


In this short article I will try to explain what is an HTTP interceptor and how to use it. My article is based on the Angular’s official documentation which I strongly recommend to take a look at, you can find it here: [intercepting-requests-and-responses](https://angular.io/guide/http#intercepting-requests-and-responses).


In case you’re wondering, the contents of this article is valid for Angular 2, 4 and 6, which now is called Angular. AngularJS is the old version, namely version 1.0


You can use HTTP interceptors to set headers (**JWT for example**), redirect on error responses and so on, this will help you avoid a lot of code duplication, I will skip the details on how to create an Angular project, instead, you’ll find the complete code on a Github repo, so feel free to continue reading this article, you can look at the complete code afterwards.


Writing an HTTP interceptor is dead simple, all you have to do is follow the following steps:


1. Create a class that implements `HttpInterceptor` then decorated it with `@Injectable`
2. Provide the interceptor.


If you’re an Angular developer you already know how provide services, there’s a catch tho, for HttpInterceptors, you must provide it in the AppModule.


You’ll also do it like this:


```
{ provide: 
HTTP_INTERCEPTORS
, useClass: NoopInterceptor, multi: true }
```


It’s a good practice to follow the instructions from the official docs and create a new array in which to put all the interceptors, so that you only have to add new interceptors to the array and then provide the array in the AppModule’s root.


The way interceptors work is that they look at the request then they call next on it. The following interceptor is a no operation interceptor, it does nothing.


```
intercept(
req
: HttpRequest, next: HttpHandler):
    Observable {
    return next.handle(req);
  }
```


As far as I know you can’t configure interceptors to activate on certain conditions, if you know that, please let me know. The way you would configure an interceptor to activate only for some routes is to inspect the **req** parameter, as seen from this example :


```
/** Simulate server replying to file upload request */
@Injectable
()
export class UploadInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest, next: HttpHandler): Observable {
    if (
req.url.indexOf('/upload/file'
) === -1) {
      return next.handle(req); // do nothing
    }
    const delay = 300;
    // Create simulation of upload event stream
    return createUploadEvents(delay);
  }
}
```


*Taken from upload-interceptor.ts*


If you have an application that uses JWT as an authentication method, you can write an HTTP interceptor that sets the JWT header if available, in the **intercept** method you would do something like the following pseudocode:


```
token = this.auth.getToken() // auth is provided via constructor.
```


```
if (token) {
    req.setHeader('Authorization', 'Bearer ' + token)
}
return next.handle(req);
```


The full code can be found here: 


If you like what I write, feel free to follow me on [Twitter](https://twitter.com/nuculabs) and on [Medium](https://medium.com/@MetonymyQT).