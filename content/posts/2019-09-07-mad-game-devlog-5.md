---
author: "Denis Nu\u021Biu"
categories:
- Game Development
- Programming
date: '2019-09-07T10:39:39+00:00'
guid: https://nuculabs.wordpress.com/?p=478
id: 478
layout: post
permalink: /2019/09/07/mad-game-devlog-5/
tags:
- csharp
- game-devlog
- unity
title: 'MAD: Game DevLog #5: Game Over'
---
![](/wp-content/uploads/2019/09/checkmate-1511866_1280.jpg?w=1024)
Image by [Steve Buissinne](https://pixabay.com/users/stevepb-282134/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1511866) from [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1511866)
Hello,


I don’t think I’d like to invest time and resources in this project anymore, I had fun programming it and learning about different aspects of game development and the Unity game engine.


Making games involves a lot of hard work and I initially started this in order to escape from my ordinary programming activities, to do something different.


Next, I think I’m going to shift my focus to some areas that will benefit the development of my career, like, trying to learn more about computer security and getting better at solving problems.


If you wanna checkout the game you can do on [itch.io](https://dnutiu.itch.io/snowpiercer-isog)


I’d like to thank Andrei, Maxi and everyone who supported this project!


Before ending this article, here’s some Unity tips we’ve found useful while working on this game:


## Make an atlas for prefabs to instantiate


Instead of setting prefabs to game objects manually in the editor, which is prone to error, you can make a static class to hold each prefab, this way can Instantiate all your prefabs programmatically.


```
    public static class PrefabAtlas
    {   
        /* Snow Walls */
        public static readonly GameObject DestructibleHighSnow =
            Resources.Load
("Walls/destructible_high_snow");
        public static readonly GameObject DestructibleSnow = 
            Resources.Load
("Walls/destructible_snow");
        public static readonly GameObject IndestructibleWoodCrate =
            Resources.Load
("Walls/indestructible_crate");
    }
```


```
Instantiate(PrefabAtlas.DestructibleSnow)
```


## Avoid classic singletons.


If you want to use classic singletons for your game logic or state then you may get things messy, depending on your specific use case, because Unity manages the lifecycle of MonoBehaviour classes.


To create a singleton in Unity you can inherit from Singleton:


```
public class MySingleton : Singleton
{
    // (Optional) Prevent non-singleton constructor use.
    protected MySingleton() { }


    // Then add whatever code to the class you need as you normally would.
    public string MyTestString = "Hello world!";
}
```


Thank you!


References:


- 