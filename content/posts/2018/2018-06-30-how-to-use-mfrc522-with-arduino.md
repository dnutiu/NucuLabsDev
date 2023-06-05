---
author: "Denis Nu\u021Biu"
categories:
- Embedded
- Programming
date: '2018-06-30T08:07:13+00:00'
guid: https://nuculabs.wordpress.com/?p=87
id: 87
layout: post
permalink: /2018/06/30/how-to-use-mfrc522-with-arduino/
title: How to use MFRC522 with Arduino
---
I wanted to explore the magic of RFID and I bought a simple card reader from Banggood which allows me to read and  
write 13.56MHz cards. The reader comes with one id tag and one card and it can be used to implement  
projects like a door security system or a payment system.


The card and id tag that come with the board use classic mifare technology.


# Wiring


I’m going to use Arduino Nano for this example, you can use Arduino UNO and perhaps other Arduino’s too. You can’t  
use any pin for the module board, you need to verify your Arduino’s pin map and map them accordingly, for example,  
you can only use SCK on pin D13.


![1.jpeg](/wp-content/uploads/2018/06/1.jpeg)


The module uses 3.3V if you connect it to 5V you may break it.


I used the following pins in my code:


- Arduino’s Pin 9 -> Module Board’s RST
- Arduino’s Pin 10 -> Module Board’s SDA
- Arduino’s Pin11 -> Module Board’s MOSI
- Arduino’s Pin12 -> Module Board’s MISO
- Arduino’s Pin13 -> Module Board’s SCK


The library works with the Arduino MKR1000 (❤ Which means RFID and IoT ❤). It works smooth. But I got some compile errors.


![2.jpeg](/wp-content/uploads/2018/06/2.jpeg)


error: invalid conversion from ‘int’ to ‘MFRC522::StatusCode’ \[-fpermissive\]


In order to fix this, you need to add a casting to the parameter that converts from int to status code:


Serial.println(mfrc522.GetStatusCodeName((MFRC522::StatusCode)status));


## Before we start programming


You first need to install a library in order to communicate with the module. The library I used can be found [here](https://github.com/miguelbalboa/rfid). If you don’t know what’s a library please use this [link](https://www.arduino.cc/en/reference/libraries) to find out more.


And if you’re an advanced user, I think it is useful to read the [README](https://github.com/miguelbalboa/rfid/blob/master/README.rst "README.rst") file and the header file [MFRC522.h](https://github.com/miguelbalboa/rfid/blob/master/MFRC522.h "MFRC522.h").


And now, let’s talk a little about how the data is stored. The cards that come with the module board can store up to 1024 bytes of information. The information is organised in 16 sectors, each sector having 4 data blocks that can hold up to 16 bytes for about 10 years.


Each sector contains three data blocks and the sector’s trailer block. The first sector contains the manufacturer’s block, which contains information about the manufacturer and only two data blocks. The manufacturer’s block is read-only.


![3.png](/wp-content/uploads/2018/06/31.png)


## Sector Trailer


The sector trailer is very important! It contains the following format:


6 bytes for Key A


4 access bytes


6 bytes for Key B


The Keys A and B are used together with the access bytes to set write/read permissions for the trailer. If you’re not careful when you’re writing to the sector trailer you might make the entire sector unusable! I’ve already broken 3 sectors.


The default values for the A and B keys are: FF FF FF FF FF FF


In the default case, the key B is not used.


The access bytes are used to set read/write permissions for the sector trailer block and the 3 data blocks. You can for example set Key A for read only and Key B for read/write. In order to keep things simple you can use this site to configure the bits: http://www.akafugu.jp/posts/blog/2015\_09\_01-MIFARE-Classic-1K-Access-Bits-Calculator/


All this information including more detail is available in the datasheet.


## Code Example


[https://gist.github.com/dnutiu/da07d36d60cc88ef5bb11b0b778da4f6 ](https://gist.github.com/dnutiu/da07d36d60cc88ef5bb11b0b778da4f6)  
The code contains comments that help you better understand it, also check the examples provided by the library. Have fun 🙂