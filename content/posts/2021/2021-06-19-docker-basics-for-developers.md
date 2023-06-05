---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2021-06-19T19:00:00+00:00'
guid: http://nuculabs.dev/?p=1325
id: 1325
layout: post
permalink: /2021/06/19/docker-basics-for-developers/
tags:
- docker
- docker-compose
title: Docker basics for Developers
---
# Introduction


Hello 🙋‍♂️


In this article we will discuss a tool called Docker 🐬


Docker is a platform which allows to package individual applications in containers. This achieves application isolation at the OS level without the need to use virtualization technologies by making use of the OS APIs.


Since it can be a little hard to get into Docker if you are new I will try to keep things short and concise.


## A little example


Let us say you want to deploy two **Applications** on a Linux box, and one of them depends on imagemagick library version A and the other one depends on version B.


Since you can have only one version of the library installed at the same time you cannot deploy both applications. ☹


![](/wp-content/uploads/2021/06/docker-err.jpg?w=400)
With Docker, you can package the **Application** and all its dependencies into containers. 📦


Something you can probably achieve without Docker as well if you try hard enough. But Docker makes this amazingly easy for us.


![](/wp-content/uploads/2021/06/docker-no-err.jpg?w=400)
Once you’ve packaged your application you can publish it on the [Docker Hub](https://hub.docker.com/search?q=&type=image&category=application_services%2Cdatabase%2Cmessaging) so other people can make use of.


Let us recap:


- 🐬Docker is a platform that empowers developers.
- ☁Docker Hub is a Hub for sharing Docker containers, tools and plugins.
- 📦Container is an abstraction at the application level.


# Installing Docker ⚙


To install Docker, follow the [official guide](https://docs.docker.com/engine/install/) it is well written.


If you are on Windows 10 Home, I recommend you install WSL and WSL2 before installing Docker.


[Install WSL on Windows 10 | Microsoft Docs](https://docs.microsoft.com/en-us/windows/wsl/install-win10)


# Packaging 📦


To package you will need a Dockerfile. The Dockerfile is a text document that contains all the steps needed to package the application into a container.


For a Golang application an example Dockerfile would look like this:


```
# Golang is our base images.
FROM golang:1.7

# Make a directory called simplFT
RUN mkdir -p /go/src/github.com/metonimie/simplFT/

# Copy the current dir contents into simplFT
ADD . /go/src/github.com/metonimie/simplFT/

# Set the working directory to simplFT
WORKDIR /go/src/github.com/metonimie/simplFT/

# Install dependencies
RUN go get "github.com/zyxar/image2ascii/ascii"
RUN go get "github.com/spf13/viper"

# Build the application
RUN go build ./main.go

# Run simplFT when the container launches
CMD ["./main", "-config-name", "docker-config"]
```


After you have a Dockerfile setup, you can build an image running: `docker build . -f path_to_dockerfile -t my_app`


Once the image is built you can use the image as a base for containers, and you can run as many as you would like: `docker run -d  -p 8080:8080 -p 8081:8081 my_app`


To see running containers you can write: `docker ps`


And to stop a container: `docker stop container_id`


You can also SSH into containers, mount volumes, expose ports, obtain logs and many more.


**
If you have secrets in the current directory!!!
**


Create a file called **.dockerignore** and include all files that contain sensitive information in it. If you do not, you may leak sensitive information into your Docker images.


## Docker and Docker-Compose


Docker-compose is another tool which allows run recipes that involve multiple containers with ease.


If you are a Developer then your application will depend on other services like Nginx, Reddit, MongoDB, RabbitMQ and so on. Having to setup your DEV environment and install all these on your machine is painful a boring, and if you install the wrong version of MongoDB your application may crash.


Just like with Dockerfile you can create a docker-compose.yaml file, in which can reference third party images or your own Dockerfile.


A local development environment for WordPress would look like this:


```
version: "3.9"
    
services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: somewordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
    
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
    ports:
      - "8000:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
```


You can run `docker-compose up -d` to start all services and `docker-compose down` to stop them.


The full example can be found at [Quickstart: Compose and WordPress | Docker Documentation](https://docs.docker.com/samples/wordpress/).


Thanks for reading! 🧾


Stay healthy and take care!