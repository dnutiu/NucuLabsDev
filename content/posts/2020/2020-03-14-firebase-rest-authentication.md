---
author: "Denis Nu\u021Biu"
categories:
- Web Development
date: '2020-03-14T16:23:50+00:00'
guid: https://nuculabs.dev/?p=883
id: 883
layout: post
permalink: /2020/03/14/firebase-rest-authentication/
tags:
- authentication
- firebase
- rest
title: Firebase REST Authentication
---
Hello,


In this article I will show you how to authenticate to Firebase and secure the databases with some simple security rules. I’m using this setup for my [NucuCar](https://github.com/dnutiu/NucuCar) project.


## Authentication


The first step is to enable the Email/Password sign in method, by going to the **Authentication** and clicking the **Sign-In Method** tab:


![](https://i0.wp.com/nuculabs.dev/wp-content/uploads/2020/03/image.png?fit=1024%2C225&ssl=1)
Next, we can add a manual user by clicking **Users** tab and **Add user**.


Now, to login with our newly created user, we make a POST request with a json body to the following endpoint:


```json
// POST https://identitytoolkit.googleapis.com/v1/accounts:signInWithPassword?key=WEB_API_KEY 
{
     "email": "your_user@email.com",
     "password": "your_password",
     "returnSecureToken": true
 }
```
Note that you need to fill in the **WEB\_API\_KEY** parameter as well! You can obtain it by clicking the settings gear next to Project Overview. The General tab should open and it should list the Web API Key.


If the login is successful you will get a response similar to the following:


```
{
  "kind": "identitytoolkit#VerifyPasswordResponse",
  "localId": "drH5pThXcuY1V2w2FSVwlaRZXEN2",
  "email": "xx.xx@xx.xx",
  "displayName": "",
  "idToken": "xx.xx.xx-xx-xx-xx",
  "registered": true,
  "refreshToken": "xx-xx-xx",
  "expiresIn": "3600"
}
```

In order to make authenticated requests you’ll have to provide the following header to all requests:

**Authentication: Bearer ${idToken}**

Keep in mind that the idToken expires in 3600 seconds and you’ll need to exchange the refresh token for a new idToken before or after it expires:

```
// https://securetoken.googleapis.com/v1/token?key=WEB_API_KEY
{
  "grant_type": "refresh_token",
  "refresh_token": your_refresh_token
}
```
## Security Rules

To secure my database I’ve used the following security rules. The don’t allow deletion and only allow creating, updating and reading documents. Deleting them is forbidden.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow delete: if false;
      allow create, update, read: if (request.auth != null && request.auth.uid != null);
    }
  }
}


```


Thanks for reading!