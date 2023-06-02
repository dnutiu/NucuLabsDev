---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Web Development
date: '2019-12-07T11:14:50+00:00'
guid: https://nuculabs.wordpress.com/?p=555
id: 555
layout: post
permalink: /2019/12/07/dark-theme-switch-with-vue-js/
tags:
- begineer
- easy
- vue.js
title: Simple Dark Theme Switch with Vue.JS
---
Hello,


In this post I’m going to show you how quickly you can add a dark theme switch to your Vue.JS application.


We’re going to start with a blank application. And then we’re going to create a dark-theme CSS file which we’re going to save in `public/css/darktheme.css`.


This is how the application looks without any CSS.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--RbE67mOi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jc58krxwoelfmrujcq8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RbE67mOi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jc58krxwoelfmrujcq8b.png)
Now, we’re going to put the following code in `darktheme.css`:


```
body {
    background-color: #2c3e50;
}

h1,h2,h3,h4,h5,h6,p {
    color: #42b983;
}
```


We can test our theme by appending the following line in the `
` of `public/index.html`


```
    <link rel="stylesheet" href="<%= BASE_URL %>css/darktheme.css">
```


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--ZAlWxG61--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dnxaam6gc25cnxkuk2zi.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZAlWxG61--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dnxaam6gc25cnxkuk2zi.png)
Now let’s make this interactive!


In `src/App.vue` we’ll add a button and the following methods:


```
    <button @click="darkThemeSwitch">Switch Theme</button>
```


```
  methods: {
    _addDarkTheme() {
      let darkThemeLinkEl = document.createElement("link");
      darkThemeLinkEl.setAttribute("rel", "stylesheet");
      darkThemeLinkEl.setAttribute("href", "/css/darktheme.css");
      darkThemeLinkEl.setAttribute("id", "dark-theme-style");

      let docHead = document.querySelector("head");
      docHead.append(darkThemeLinkEl);
    },
    _removeDarkTheme() {
      let darkThemeLinkEl = document.querySelector("#dark-theme-style");
      let parentNode = darkThemeLinkEl.parentNode;
      parentNode.removeChild(darkThemeLinkEl);
    },
    darkThemeSwitch() {
      let darkThemeLinkEl = document.querySelector("#dark-theme-style");
      if (!darkThemeLinkEl) {
        this._addDarkTheme()
      } else {
        this._removeDarkTheme()
      }
    }
```


Whenever we click on the `Switch Theme` button, the dark theme should switch back and forth.


This is a quick and dirty way to add a dark theme switch to your Vue.JS application. You can can also take this further by implementing a theme service and persistence support.


Thank you for reading!