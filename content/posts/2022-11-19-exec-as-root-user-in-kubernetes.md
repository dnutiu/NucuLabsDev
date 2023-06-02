---
author: "Denis Nu\u021Biu"
categories:
- Cloud
- Tutorial
date: '2022-11-19T16:47:12+00:00'
guid: https://nuculabs.dev/?p=2418
id: 2418
layout: post
permalink: /2022/11/19/exec-as-root-user-in-kubernetes/
tags:
- exex
- kubernetes
- pod
- root
- user
title: Exec as root user in Kubernetes
---
Hi 👋,


In this short tutorial I will show you a way of getting a root shell in containers running inside a modern Kubernetes cluster.


Prerequisites:


- **Root access** to the cluster node in which the container is running.


## Problem Statement


We wan’t root access into a running container, exec gives us non-root user.


```
➜  Downloads k get pods
NAME                     READY   STATUS    RESTARTS   AGE
my-release-cassandra-0   1/1     Running   0          2m9s

➜  Downloads k exec -it pod/my-release-cassandra-0 -- /bin/bash
I have no name!@my-release-cassandra-0:/$ whoami
whoami: cannot find name for user ID 1001
I have no name!@my-release-cassandra-0:/$ touch test
touch: cannot touch 'test': Permission denied
I have no name!@my-release-cassandra-0:/$ 
```


## Solution


To obtain root access. First grab the Container ID from inside the pod.


```
k describe pod my-release-cassandra-0
```


```
Containers:
  cassandra:
    Container ID:  containerd://8fa7af3900d556aa8a91b1ac4cbe46335e8df233f8645b0a2329b2f0e6d76177
    Image:         docker.io/bitnami/cassandra:4.0.7-debian-11-r0
```


Then if it the id starts with **containerd://** run the following command on the node the pod is running:


```
sudo runc --root /run/containerd/runc/k8s.io/ exec -t -u 0 8fa7af3900d556aa8a91b1ac4cbe46335e8df233f8645b0a2329b2f0e6d76177 /bin/bash
```


You should get a root shell into the Cassandra container:


```
root@my-release-cassandra-0:/# whoami
root
root@my-release-cassandra-0:/# touch test
root@my-release-cassandra-0:/# ls
bin	 boot  docker-entrypoint-initdb.d  etc	 lib	media  opt   root  run.sh  srv	test  usr
bitnami  dev   entrypoint.sh		   home  lib64	mnt    proc  run   sbin    sys	tmp   var
```


Thanks for reading and happy cloud surfing! 🏄