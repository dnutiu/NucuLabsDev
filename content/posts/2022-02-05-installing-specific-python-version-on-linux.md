---
author: "Denis Nu\u021Biu"
categories:
- Linux
- Programming
- Python
- Tutorial
date: '2022-02-05T16:07:56+00:00'
guid: https://nuculabs.dev/?p=2015
id: 2015
layout: post
permalink: /2022/02/05/installing-specific-python-version-on-linux/
tags:
- compiling python
- deadsnakes
- installing python
- pyenv
title: "How to install a\_specific Python version on Linux"
---
Hello, 👋


In this article I will show you how to install Python versions on Linux using the following methods: **compiling from source**, **dead snakes ppa** and **pyenv**.


To make things easier, if you want to follow along in an environment that you can break, you can create a local Kubernetes cluster using[ Minikube](https://minikube.sigs.k8s.io/docs/).


Next, I’m going to use the following yaml file to create an Ubuntu pod:


```
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu
  labels:
    app: ubuntu
spec:
  containers:
  - image: ubuntu
    command:
      - "sleep"
      - "604800"
    imagePullPolicy: IfNotPresent
    name: ubuntu
  restartPolicy: Always
```


Save the above yaml in a file **ubuntu\_pod.yaml** and run:


```
kubectl apply -f ./ubuntu_pod.yaml
```


To get a shell on the Ubuntu pod, run:


```
kubectl exec -it ubuntu -- /bin/bash
```


To start from scratch, simply delete the pod with **kubectl delete pod/ubuntu** and then recreate it.


## Compiling Python from source


Before compiling Python, you will need to setup the build environment, thankfully, it is straightforward.


Pyenv has great instructions on it: 
.


On Ubuntu, to build Python, install the following packages:


```
apt-get update; apt-get install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```


Then, search the desired python version [here ](https://www.python.org/ftp/python/)and, for example to install Python 3.9, run:


```
wget https://www.python.org/ftp/python/3.9.9/Python-3.9.9.tgz
tar -xzf Python-3.9.9.tgz
cd Python-3.9.9
```


Then, run configure:


```
./configure --enable-optimizations
```


And finally run **make install** if you want to replace the default Python installation or **make altinstall** to install python under the binary name of **python3.9**


make altinstall


To test the installation run:


```
python3.9 --version
Python 3.9.9


pip3.9 --version
pip 21.2.4 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)
```


## Installing Python via a third party PPA deadsnakes


To install Python using the deadsnakes ppa run:


```
apt-get update
apt-get install software-properties-common
add-apt-repository ppa:deadsnakes/ppa
apt-get update
apt install python3.9 python3-pip
```


Then, to test the installation run:


```
root@ubuntu:/# python3.9 --version
Python 3.9.10


root@ubuntu:/# python3.9 -m pip --version
pip 20.0.2 from /usr/lib/python3/dist-packages/pip (python 3.9)
```


## Installing Python via Pyenv


I already written an article on how to install Python using Pyenv, check it out if you wish.


https://nuculabs.dev/posts//2020/06/27/pyenv-for-linux-users/


Thanks for reading! 📚