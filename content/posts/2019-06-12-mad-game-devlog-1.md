---
author: "Denis Nu\u021Biu"
categories:
- Game Development
- Programming
date: '2019-06-12T07:41:31+00:00'
guid: https://nuculabs.wordpress.com/?p=435
id: 435
layout: post
permalink: /2019/06/12/mad-game-devlog-1/
tags:
- gamedev
- unity
title: 'MAD Game DevLog #1'
---
Hello Everyone!


In this devlog I’ll try to keep you updated with the latest state of our game. Since we all have full-time jobs, we’ve been working on this game only on our free time.


I’ve been implementing the world generation code and since I hadn’t any Sprites for the walls available, I’ve made my own ones in Aseprite.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--kx-W0FHv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x9to3149mg2foghf89sn.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kx-W0FHv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x9to3149mg2foghf89sn.png)
Using the Sprite from above I’ve created two prefabs in Unity and I’ve changed the colour of one prefab to brown, the generated world looks like this:


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--bTDq43MY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f9xknt4gbwcrr7qp83th.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bTDq43MY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f9xknt4gbwcrr7qp83th.png)
The player sprite is also my doing.


A few days later I’ve received the wall sprites from Max and I imported them in Unity.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--zXMFG1Rj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gxp0vxnpdc63m1k4145o.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zXMFG1Rj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gxp0vxnpdc63m1k4145o.png)
The reason the sprites look so small is that Max designed them so they can overlap but we’re not exactly sure how we’re going to do that, since this game is based on a matrix grid adding overlapping sprites to it might make it a bit confusing.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--8h-UMEqY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ofa11ul3irl4nygzshbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8h-UMEqY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ofa11ul3irl4nygzshbe.png)
Here are some pictures on how the game would look like with overlapping.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--5Q8b_fp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxohuosgcd2jb9sfhjm7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Q8b_fp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jxohuosgcd2jb9sfhjm7.png)
[![](https://res.cloudinary.com/practicaldev/image/fetch/s--fTMxLfal--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lj23ruge3z3eyrcdd2jj.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fTMxLfal--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lj23ruge3z3eyrcdd2jj.png)
Andrei took the heaviest task to get the bombs working. He was struggling with Raycasting, Coroutines and the 2D Physics system from Unity. After some time he managed to get it working. The bombs are placed and explosions are spawned, it still needs a bit of polish.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--NpmICGcu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bfzq62uy8gqhppqc0nph.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NpmICGcu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bfzq62uy8gqhppqc0nph.png)
Thanks for reading and have a nice day!


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--x2HPH2qb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4t30ab747l8bp7kwzi3q.gif)](https://res.cloudinary.com/practicaldev/image/fetch/s--x2HPH2qb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4t30ab747l8bp7kwzi3q.gif)