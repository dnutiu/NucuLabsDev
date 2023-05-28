---
author: "Denis Nu\u021Biu"
categories:
- Cloud
- Embedded
- Programming
date: '2020-02-16T21:45:42+00:00'
guid: https://nuculabs.dev/?p=839
id: 839
image: /wp-content/uploads/2020/02/8a37c-warm-and-cozy-1975215_640.jpg
layout: post
permalink: /2020/02/16/firebase-rest-on-raspberry-pi/
tags:
- dotnet
- firebase
- grpc
- Raspberry Pi
title: Firebase Rest on Raspberry Pi
---
I’ve tried to use Google’s Firebase with the Firebase Admin SDK in my C# project. The good news is that the Admin SDK is easy to use and all you need to do is call two methods. The authentication part is handled by an environment variable which points to some .json file which you download. The bad news is that the SDK depends on gRPC and the dotnet version of it doesn’t work well on the Raspberry Pi.


What I’m going to do is to replace the admin SDK with simple REST API calls, this way I can post data to Firebase from anywhere, at this moment I only want to post data from the Raspberry Pi.


I’ve been browsing the [REST API Reference](https://firebase.google.com/docs/firestore/reference/rest/) docs in in order to figure it out how to make requests and after some time of experimenting and reading the documentation I’ve translated by hand a simple telemetry request from my Raspberry Pi into a restful Firebase request.


This is how it looks like in the Firebase format:


PATCH https://firestore.googleapis.com/v1/projects/**project\_name**/databases/(default)/documents/**collection\_name/doc\_id**


```
```json
{
	"name": "batman",
	"fields": {
		"source": {
			"stringValue": "NucuCar.Sensors"
		},
		"timestamp": {
			"timestampValue": "2019-12-01T23:26:13.5537227+02:00"
		},
		"data": {
			"arrayValue": {
				"values": [
					{
						"mapValue": {
							"fields": {
								"sensor_state": {
									"integerValue": 2
								},
								"cpu_temperature": {
									"doubleValue": 48
								},
								"_id": {
									"stringValue": "CpuTemperature"
								}
							}
						}
					},
					{
						"mapValue": {
							"fields": {
								"sensor_state": {
									"integerValue": 2
								},
								"temperature": {
									"doubleValue": 32
								},
								"humidity": {
									"doubleValue": 100.0
								},
								"pressure": {
									"doubleValue": 6222
								},
								"voc": {
									"doubleValue": 0.0
								},
								"_id": {
									"stringValue": "Bme690-Sensor"
								}
							}
						}
					}
				]
			}
		}
	}
}
```
```


All what’s left to do now is to write a translator which translates raw C# dictionaries into the format from above. I want to reuse this without much hassle if my telemetry data format changes.


After a bit of trial and error I’ve managed to implement a [translator](https://github.com/dnutiu/NucuCar/blob/master/NucuCar.Domain/Utilities/FirebaseRestTranslator.cs), thus completely dropping the dependency for Firebase’s Admin SDK. I haven’t implemented authentication handling yet, but it’s on my board.


Thank you for reading!