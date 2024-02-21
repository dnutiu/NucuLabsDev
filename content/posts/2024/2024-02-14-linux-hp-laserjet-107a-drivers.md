---
title: "How to install HP LaserJet 107a Drivers on Linux"
author: [ "Denis Nutiu" ]
date: '2024-02-21'
categories:
- Tutorial
- Linux
permalink: /2024/02/21/linux-hp-laserjet-107a-drivers
tags:
  - printer
  - drivers
  - linux
  - fedora
  - ubuntu
layout: post
published: true
---

Hello everyone! 👋

I've recently installed [Fedora](https://fedoraproject.org/) on my main PC and I wanted to write this post
to serve me as a memo on how to install the HP LaserJet 107 printer driver.

The driver should also be compatible with the following products and generally HP's LaserJet 100 printer series.

- HP LaserJet 107a
- HP LaserJet 105a
- HP LaserJet 103a

## Installing the Drivers

Installing the drivers requires Python3 available on your system if I remember correctly, it should be already
available on Fedora and Ubuntu.

To install the drivers run the following commands:

```shell
wget https://ftp.hp.com/pub/softlib/software13/printers/CLP150/uld-hp_V1.00.39.12_00.15.tar.gz 

tar -xvf uld-hp_V1.00.39.12_00.15.tar.gz 

cd uld/ 
sudo ./install.sh 
```

If you don't like the terminal you can also download the driver from HP's [website](https://support.hp.com/us-en/drivers/hp-laser-100-printer-series/24494339).

## Installing the Printer

I'm using Gnome Software v45 and if you're using Gnome as well you can follow these steps to install the printer.

1. Connect your printer to your PC.
2. Go to Settings -> Printers
3. Click add a printer and select your printer.
4. **<span style="color:red">IMPORTANT</span>** In the printer details, the driver should be `HP Laser 10x Series`.

If the driver is not `HP Laser 10x Series` then click on the `Select from Database...` button,
select Hewlett-Packard as the manufacturer in the left tab and search for the `HP Laser 10x Series` driver in the righ
tab then select it.

You should be able to print your documents. 😁

Thank you for reading! 📚