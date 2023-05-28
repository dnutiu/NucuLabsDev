---
author: "Denis Nu\u021Biu"
categories:
- Game Development
- Programming
date: '2019-06-23T19:47:23+00:00'
guid: https://nuculabs.wordpress.com/?p=439
id: 439
layout: post
permalink: /2019/06/23/mad-game-devlog-2/
tags:
- gamedev
title: 'MAD: Game DevLog #2'
---
Hello everyone! Time for devlog number #2!


In the last week, we’ve implemented transparent walls and the player animation state machine. Max continued designing sprites for the game and Andrei implemented the bomb animations and it is working on some enemies. The next week I will try to implement and finish the UpgradeManager, a Singleton class that shall handle the dispatch player powerups.


The transparent walls were implemented using Raycasts the wall will cast a ray in the up direction and if it detects the Player collider it will reduce it’s alpha value to 50%, when the ray doesn’t collide with anything the alpha values goes up to 100%. In the following picture the raycast is drawn with a red line:


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--UwpJjv_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sc4rr403hp26qqv8x99v.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UwpJjv_m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sc4rr403hp26qqv8x99v.png)
When I first attempted to do the player animation state machine I got lost very quickly and I quickly abandoned the idea. I tried implementing it with a blending tree, hoping that it would work that way but it didn’t.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--oBBkVWuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1t8xd74bxwiks2uswlcl.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oBBkVWuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1t8xd74bxwiks2uswlcl.png)
Blend Trees are used to blend multiple animations smoothly, when you have a walking/running animation in 2D you’d like to play the “WalkLeft” animation when X is -1 and Y is Don’tCare. To achieve this Unity gives us blend trees:


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--LYW34jso--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36nir5qk9d2bjwfogxdy.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LYW34jso--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36nir5qk9d2bjwfogxdy.png)
The problem with this approach is that it doesn’t work when you have idleLeft and runLeft, because the blend tree doesn’t know what idle to pick when both X and Y are 0. After taking a break the solution just popped into my head: Use a state machine instead of a blend tree. From any state you can go into the RunX state but after you’re done with the RunX state you’ll transition to IdleX.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--ijkLYY4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bwarto50aapwym8dn9mq.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ijkLYY4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bwarto50aapwym8dn9mq.png)
This works as expected but with a tradeoff, you can’t move in diagonals. Each transition has a rule for AnimationX or AnimationY attached to it, and if you change both X and Y at the same time you randomly jump to RunX and RunY animations, making it look like a glitch.


In Unity X and Y are custom parameters for the state machine, it doesn’t necessarily need to be the X and Y position of the game object. You can tweak your movement handling code to set AnimationX and AnimationY to prefer the max of those two and set the other to 0.


To work collaboratively on this project we use Github for code and Google Drive for assets sharing.


[![](https://res.cloudinary.com/practicaldev/image/fetch/s--RSdZWQbC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a333mkmfq80oxre7y4kt.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RSdZWQbC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a333mkmfq80oxre7y4kt.png)
If you want to use Github for your Unity project I’ll recommend that you generate a proper [.gitignore](https://www.gitignore.io/) file.


Thanks for reading!


In case you’re wondering from where the title MAD comes it just comes from our names: Maxi, Andrei and Denis. Since this is our first game and out first attempt in doing something together I had no idea what name to chose.


If you wanna start developing your first game with Unity, we’ve found the following video lectures to be quite motivational and helpful: 