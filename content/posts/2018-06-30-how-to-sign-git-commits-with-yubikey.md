---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2018-06-30T07:33:41+00:00'
guid: https://nuculabs.wordpress.com/?p=72
id: 72
layout: post
permalink: /2018/06/30/how-to-sign-git-commits-with-yubikey/
publicize_twitter_user:
- denisnutiu
- denisnutiu
- denisnutiu
- denisnutiu
tags:
- github
- yubikey
title: How to sign git commits with Yubikey
---
# Introduction


If you know what a Yubikey is, you may skip this section.


Yubikey is quite expensive device that acts as a portable GPG key, it is considered secure because once the private GPG key is on the device, it can’t be extracted again (unless someone finds a way).


You can import your own generated GPG keys or generate a new key-pair directly on Yubikey.


Yubikey can do a lot more than holding a GPG key, It can do two-factor authentications with sites like Google, Github or Dropbox. The list of all supported sites are available on their official website. You can also use them with your mobile phone, if you have NFC, when you login to Google using your credentials, if NFC is on, the phone asks you to put the Yubikey on the NFC sensor so it can communicate with it, if you don’t have NFC the website will fall back to another two-step authentication method that you have enabled for your account.


You can also use Yubikey to login to remote ssh machines or to login on your laptop, but that’s a bit risky, if you have only one key, because if you lose the key, you are locket out of your own devices and servers unless you have a second backup Yubikey.


You can pretty much personalize your Yubikey using `gpg --card-edit` when your Yubikey is plugged in. Here’s the output of the command on my machine:


![1.png](/wp-content/uploads/2018/06/1.png)


## Singing git commits


If you want to have a fancy badged saying “Verified” next to your commit then follow these steps:


1. Generate your GPG key-pair on Yubikey. Run: `gpg --card-edit` and then `admin` to allow admin commands. See if you can find the right command using `help` in order to generate your keys.
2. Go to [Github->Settings->Security](https://github.com/settings/keys) and add your public key there.


![2.png](/wp-content/uploads/2018/06/2.png)


1. Configure the signing key.


 
4\. Verify your commits when committing with the `-S` flag. If you already have a commit you can type: `git commit --amend -S --no-edit` in order to verify it.


1. Push the commit.


![3.png](/wp-content/uploads/2018/06/3.png)


The “Verified” badge will prove, that you and only YOU have made the commit and if someone hijacks your github account and tries to push a malicious commit, they can do that but they can’t verify the commit, thus, if you’re working on a high-security project, you and your team may have set some rules that every commit must be verified with a personal GPG key.


If you like what I’m blogging about, you may follow me on Twitter


#### Thank you for reading!