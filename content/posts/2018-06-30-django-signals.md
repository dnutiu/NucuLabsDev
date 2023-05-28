---
author: "Denis Nu\u021Biu"
categories:
- Python
- Web Development
date: '2018-06-30T07:38:09+00:00'
guid: https://nuculabs.wordpress.com/?p=77
id: 77
layout: post
permalink: /2018/06/30/django-signals/
publicize_twitter_user:
- denisnutiu
- denisnutiu
- denisnutiu
- denisnutiu
tags:
- django
title: Django Signals
---
I’ve been working on a website for some time now, at first I’ve made it with Django + templates and now I use Django Rest Framework and Angular.  
The website is supposed to be a platform for uploading papers and offer the ability to peer review them.


What I want to talk about in this short article is how I simplified the development using Django signals for certain use cases.


The official docs describe signals as:


> Django includes a “signal dispatcher” which helps allow decoupled applications get notified when actions occur elsewhere in the framework. In a nutshell, signals allow certain senders to notify a set of receivers that some action has taken place. They’re especially useful when many pieces of code may be interested in the same events.


In my website I needed to use signals in the following situations:


- Create a profile for every user.
- Update model when something changed.


## Create a profile for every user.


I have a profile model already defined, let’s ignore it.


\[code language=”python”\]  
@receiver(post\_save, sender=settings.AUTH\_USER\_MODEL)  
def create\_profile(sender, instance, created, \*\*kwargs):  
“””  
Ensure that every new user gets a profile.  
“””  
if created:  
Profile.objects.create(user=instance)  
\[/code\]


That was the signal that creates a new profile for every user that is created, pretty easy, right? I will not even bother explaining the code.  
At first I was overriding the create function from a custom serialiser, but that only worked when I was creating users via API calls, It didn’t work when the admin created new users, overriding save method of the model was cumbersome and I avoided it.


## Update model when something changed.


Here’s the code for the other two signals:


\[code language=”python”\]  
@receiver(pre\_save, sender=Paper)  
def editor\_field\_changed(sender, instance, \*\*kwargs):  
“””  
If there’s an editor and paper status is processing. Change the status to under\_review.  
If there’s no editor and the paper status is under\_review, change the status to processing.  
“””  
if instance.editor and instance.status == Paper.STATUS\_CHOICES\[0\]\[0\]:  
instance.status = Paper.STATUS\_CHOICES\[1\]\[0\] # under\_review  
elif instance.editor == None and instance.status == Paper.STATUS\_CHOICES\[1\]\[0\]:  
instance.status = Paper.STATUS\_CHOICES\[0\]\[0\] # processing  
\[/code\]


Basically, what this does is to set the paper status to under\_review when an editor is assigned to it and the paper’s current status is processing.  
Notice that the signal is transmitted at the pre\_save event by Paper model.


\[code language=”python”\]  
@receiver(post\_save, sender=Review)  
def reviews\_changed(sender, instance, \*\*kwargs):  
“””  
If there’s an editor review, set the paper status according to the result of the editor review.  
“””  
if instance.editor\_review:  
\# If the editor review is positive, change the paper status to published.  
if instance.appropriate == Review.APPROPRIATE\_CHOICES\[0\]\[0\] \\  
and instance.recommendation == Review.RECOMMENDATION\_CHOICES\[1\]\[0\]:  
instance.paper.status = Paper.STATUS\_CHOICES\[3\]\[0\] # accepted  
else:  
instance.paper.status = Paper.STATUS\_CHOICES\[2\]\[0\] # preliminary\_reject  
instance.paper.save()  
\[/code\]


This signal is transmitted at post\_save by the Review model, it sets the paper status to accepted or preliminary\_reject. The review instance has a paper object so the models are tied together.


Thank you, I hope this helped you!  
If you have any questions feel free to comment below or ask me on twitter.


### Resources


- https://docs.djangoproject.com/en/2.0/topics/signals/
- https://docs.djangoproject.com/en/2.0/ref/signals/#