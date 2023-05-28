---
author: "Denis Nu\u021Biu"
categories:
- Uncategorized
date: '2022-10-23T17:42:52+00:00'
guid: https://nuculabs.dev/?p=2373
id: 2373
layout: post
permalink: /2022/10/23/envsubst-substitute-variables-for-environment-variables/
tags:
- envsubst
- Linux
- substituting configuration files values with env variables
- Tooling
- Tutorial
title: envsubst &#8211; Substitute Variables for Environment Variables
---
Hi 👋,


## Introduction


In this short article I want to showcase a nice and useful Linux command.


The comand 🥁 **envsubst** 🥁. In short it **Substitutes the values of environment variables.**


**It’s great for populating configuration files with values from environment variables**, a common operation for developers containerizing their applications.


## Example Usecase


Let’s say we are containerizing an application and we have the following file **configuration.yaml**, and we want to modify the values of the **environment** field and the **log\_level** field without adding the additional complexity of mounting the configuration.yaml file into the container/pod.


```
configuration:
  server: "the-app"
  environment: "production"
  log_level": "info"
```


To change the values of the **environment** and **loglevel** fields we create a **configuration-template.yaml** like so:


```
configuration:
    server: "the-app"
    environment: "$ENV_ENVIRONMENT"
    log_level": "$ENV_LOGLEVEL"
```


Then we run eventsubt to substitute the configuration values:


```
export ENV_ENVIRONMENT=stagging
export ENV_LOGLEVEL=trace


envsubst < configuration-template.yaml > configuration.yaml
```


The resulting **configuration.yaml file** will contain:


```
configuration:
    server: "the-app"
    environment: "stagging"
    log_level": "trace"%  
```


## Conclusion


The **envsubst** command enables us to write configuration files with placeholders that will be replaced with actual values from the environment variables. Before being aware if this command I always thought that I have to somehow parse the files, substitute variables dynamically, ensure that the substitution didn’t change the file format or break something. Now I just **envsubst** .


Thanks for reading and happy dev-ing! 🖥️🧑‍💻


### envsubst help


Usage: envsubst \[OPTION\] \[SHELL-FORMAT\]


Substitutes the values of environment variables.


Operation mode:  
-v, –variables output the variables occurring in SHELL-FORMAT


Informative output:  
-h, –help display this help and exit  
-V, –version output version information and exit


In normal operation mode, standard input is copied to standard output,  
with references to environment variables of the form $VARIABLE or ${VARIABLE}  
being replaced with the corresponding values. If a SHELL-FORMAT is given,  
only those environment variables that are referenced in SHELL-FORMAT are  
substituted; otherwise all environment variables references occurring in  
standard input are substituted.


When –variables is used, standard input is ignored, and the output consists  
of the environment variables that are referenced in SHELL-FORMAT, one per line.


Report bugs in the bug tracker at 
 
or by email to 
.