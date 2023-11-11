---
title: "Windows Task Scheduler - Quick Start"
author: [ "Denis Nutiu" ]
date: '2023-11-11'
categories:
  - "AI"
  - "Programming"
  - "Windows"
permalink: /2023/11/11/windows-task-scheduler/
tags:
  - programming
  - machine learning
  - tutorial
layout: post
---

# Introduction

Hello everyone 👋,

This is a quick post about the windows task scheduler, if you're a Software Developer using Windows the task scheduler
is a great tool to automate tasks. I see it as a combination of cron and systemd (if you're a Linux user you know what I'm talking about).

For my personal use case, I use the task scheduler to download more images from the internet to improve a machine
learning model used to classify images. To download the images I use a simple Python script and a batch file to run the script.

Let's get back to the Task Scheduler and how to use it.

# Using the Task Scheduler

To open the Task Scheduler, press the Windows key and type "Task Scheduler" and press enter.

![Task Scheduler](/hugo-content/2023-11/windows-task-scheduler.png)

Once the Task Scheduler is open, you can create a New Folder for your custom tasks by clicking `New Folder` in the
actions panel on the right. Then you can create a new task by clicking `Create Task...`.

You can then give the Task a name and a description.

## Configuring the Task.

The task scheduler supports plenty configuration options to customize the task scheduling and running.

### Actions

Switch to the Actions tab and click `New...` to create a new action. Then select the start a program option and 
point it to your program and working directory.

![Task Scheduler](/hugo-content/2023-11/windows-task-scheduler-action.png)

### Triggers

For the triggers, I have a single trigger configured `At logon`, since I want the task to run when I logon to my computer.

You can also run the task on a schedule, on an event, on start-up, on idle and so on.

I've also added a task delay of one minute to ensure that the network is available.

### Conditions

Conditions are used to restrict the task from running in certain conditions.

The only condition that I've configured is the availability of the network. You can also configure the task to be run
only when the computer is idle, or when the computer is on AC power, for example.

### Settings

The settings tab is used to configure the task behavior. I've configured the task to run as soon as possible if the
task is missed and to stop the task if it runs longer than 1 hour.

# Conclusion

The Windows Task Scheduler is a great tool to automate tasks on Windows. It's easy to use and it has plenty of
configuration options to customize the task scheduling and running.

I hope you found this post useful and I'll see you in the next one.

Cheers 🍻.

# References

- [Windows Task Scheduler](https://en.wikipedia.org/wiki/Windows_Task_Scheduler)
- [Task Scheduler Start Page](https://docs.microsoft.com/en-us/windows/win32/taskschd/task-scheduler-start-page)

