---
author: "Denis Nu\u021Biu"
categories:
- Game Development
date: '2019-08-09T20:24:52+00:00'
guid: https://nuculabs.wordpress.com/?p=458
id: 458
layout: post
permalink: /2019/08/09/mad-game-devlog-4-main-menu-camera-shake-and-enemies/
tags:
- c#
- smeltgames
- unity
title: 'MAD: Game DevLog #4, Main Menu, Camera Shake and Enemies'
---
Hello everyone!


Here’s what we did for our 4th devlog:


- - - - - -


We’ve added a game menu to the game it is interactive and it’s working without any code at all. Thanks to Brackeys for making awesome tutorials, here’s the one we used for the menu: [https://www.youtube.com/watch?v=zc8ac\_qUXQY](https://www.youtube.com/watch?v=zc8ac_qUXQY)


- - - - - -


After creating the main menu, we wanted to juice up the game a little and we’ve researched a ways to shake the camera when a bomb explodes.


The fist approach was naive and it didn’t work. I was taking the camera’s position and moving it randomly, which felt robotic. Luckily for us, Unity has a built in perlin noise generator for [Cinemachine](https://unity.com/unity/features/editor/art-and-design/cinemachine), the package we use for managing cameras.


The Perlin noise was developed by Ken Perlin and it is:


> a technique used to produce natural appearing textures on computer generated surfaces for motion picture visual effects.


Unity also lets us tune the generator by modifying its amplitude and frequency parameters, which makes it very convenient to use.


- - - - - -


Last but not least we weren’t satisfied with the enemies, we still have some bugs with their behavior but Andrei was there to fix them.


What I did instead was to try to make some decent enemy sprites in Aseprite, Cosmigo Pro Motion doesn’t work on my Mac :(.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--hXrZvSWU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2c63qusqinlosn1hlst.gif)](https://res.cloudinary.com/practicaldev/image/fetch/s--hXrZvSWU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c2c63qusqinlosn1hlst.gif)
Snow Enemy v1
Since I don’t have enough practice my sprites don’t look that good, I tried to keep them as simple as possible and I’ve only used two key frames when animating them.


If you want to start making Pixel Art there are lot of free tutorials on the internet. Here’s some to get you started:


- 
- 


- - - - - -


Thanks for reading and have a nice day!