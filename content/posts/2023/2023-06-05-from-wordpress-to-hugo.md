---
author: ["Denis Nutiu"]
categories:
- "Web Development"
date: '2023-06-05'
layout: post
permalink: /2023/06/05/from-wordpress-to-hugo
tags:
- wordpress
- hugo
- "static website"
title: "From Wordpress To Hugo"
---

# Introduction

Hello everyone 👋,
    
This is a quick post on how I migrated my blog from WordPress to Hugo. 
I will not go into details on how to install Hugo or how to use it, 
but I will share some of the things I learned along the way.

I've also written a simple tool in Python to aid you in the migration process. 
You can find it here: https://dnutiu.github.io/jekyll-to-hugo/

# Why not WordPress?

I've been using WordPress for about 5 years now, and I've switched from free hosted WordPress to self-hosted
and then to premium hosted WordPress.

What I don't like about WordPress is that the quality of free hosted blogs went down, for example you cannot
have plugins, you're not in control of your blog, they might delete it without warning, and you have
ads and banners on your blog.

The cheaper premium version gives you limited theming options, and it is **required** if you want 
a custom domain for your blog. The more expensive version of premium WordPress gives you more features,
but it's not worth it for me and my use-cases.

The open-source version of WordPress is even worse, you have **basic** blogging functionality but if you want to
protect yourself from **Spam** you'll need to install [JetPack](https://cloud.jetpack.com/pricing) which is free for
**basic** features and everything else is paid. Plugins for SEO, Code Highlight, are free for *basic* features
and when you need something more complete you'll need to pay for it.

In the end paying for hosting, managing the database, handling backups and upgrades, subscription to wp theme,
paying for plugins  not worth it at all, you'll quickly reach tons of $$ per month only by paying for
micro features that should be included and plugins... 💸 + 💸= 🤑.

And, if you decided that you'll use the free version of plugins each plugin will add a banner to your WP Admin
dashboard in order to *"remind you"* to upgrade 💰. 

Every time I wanted to write a blog post I was greeted with ads and reminded to "upgrade"
and that was a huge turnoff for me! 🤮

![billboards and ads](/hugo-content/2023-05/pexels-vlad-alexandru-popa-1486222.jpg)

WordPress is not for me anymore.

# Why Hugo?

Hugo gave me complete control over my content, and now I can write my posts directly from my IDE
in markdown 😎. I've also found a great *free* theme [hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)
which can be customized fully only with basic JavaScript, HTML and CSS.

It also has lots features 😳. Such as: Posts, Categories, Tags, Table of Contents, Search and Syntax Highlighting.

The content is also static, that means a fast website and easy hosting. 🌥️

# Migration

To migrate your blog you'll need to install a plugin called [jekyll-exporter](https://wordpress.org/plugins/jekyll-exporter/).
It will export your posts into a Jekyll post format, which then can be converted to Hugo format.

Then I've used the [Jekyll to Hugo](https://dnutiu.github.io/jekyll-to-hugo/) tool to migrate my blog from WordPress to Hugo.

Since my blog was hosted on WordPress.com and I had the cheaper premium plan
that did not support plugin installations I had to improvise.

Here's a demonstration video:

{{< youtube wFkPMxhwoK8 >}}
---

### Docker-Compose

The docker compose file is used to the blog inside a container:

```yaml
version: '3.1'

services:

  wordpress:
    image: wordpress
    restart: always
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
    volumes:
      - wordpress:/var/www/html
      - "./php.ini:/usr/local/etc/php/conf.d/php.ini"

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql

volumes:
  wordpress:
  db:
```

The `php.ini` file in order to override upload defaults.

```ini
upload_max_filesize = 10M
post_max_size = 10M
```

Thank you for reading and happy blogging! 📚 