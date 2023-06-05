---
author: "Denis Nu\u021Biu"
categories:
- Golang
- Programming
date: '2022-10-17T12:18:24+00:00'
guid: https://nuculabs.dev/?p=2375
id: 2375
image: /wp-content/uploads/2022/10/pexels-photo-735978.jpeg
layout: post
permalink: /2022/10/17/using-confluent-kafka-go-on-macos-m1/
tags:
- apache kafka
- building for macOS-arm64
- building for macOS-arm64 but attempting to link with file built for macOS-x86_64
- confluent-kafka-go
- go
- librdkafka_darwin.a
- macOS-arm64
- macOS-x86_64
title: Using confluent-kafka-go on MacOS M1
---
Hello,


**TLDR**;


```
brew install librdkafka openssl@3 pkg-config
export PKG_CONFIG_PATH="/opt/homebrew/opt/openssl@3/lib/pkgconfig"
go test -tags dynamic ./...
```
I’ve been transition from a Linux machine to a MacOS M1 machine at work and when I ran tests for a Golang project, I noticed that the test failed on modules depending on **librdkafka**.


Initially I’ve had problems with Kafka on MacOS M1 on Docker, since I was using an older image version that didn’t have any arm64 build, updating to images to version 7.2.1\[1\] fixed the issues.


This time however the problems was with my Golang dependencies and not the Docker containers. Running the tests resulted in:


```
go test ./...
[...]
ld: warning: ignoring file /Users/dnutiu/go/pkg/mod/github.com/confluentinc/confluent-kafka-go@v1.8.2/kafka/librdkafka_vendor/librdkafka_darwin.a, building for macOS-arm64 but attempting to link with file built for macOS-x86_64
Undefined symbols for architecture arm64:
```

“**Undefined symbols for architecture arm64**” My guess is that the published confluent-kafka-go package does not contain (yet) symbols for arm64, to fix the issues you can use the module with another librdkafka.

When installing [librdkafka](https://formulae.brew.sh/formula/librdkafka) formula from Homebrew the library is built for arm64 architecture. To install run:

```
brew install librdkafka openssl@3 pkg-config
```
Next, we’ll use a tool called **[pkg-config](https://www.freedesktop.org/wiki/Software/pkg-config/)** to tell librdkafka where to find the other libraries, since it depends on openssl we need to export PKG\_CONFIG\_PATH. To grab the value run:

```
brew info openssl
==> openssl@3: stable 3.0.5 (bottled) [keg-only]
Cryptography and SSL/TLS Toolkit
https://openssl.org/
/opt/homebrew/Cellar/openssl@3/3.0.5 (6,444 files, 27.9MB)
  Poured from bottle on 2022-08-31 at 14:10:49
From: https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/openssl@3.rb
License: Apache-2.0
==> Dependencies
Required: ca-certificates ✘
==> Caveats
A CA file has been bootstrapped using certificates from the system
keychain. To add additional certificates, place .pem files in
  /opt/homebrew/etc/openssl@3/certs


and run
  /opt/homebrew/opt/openssl@3/bin/c_rehash


openssl@3 is keg-only, which means it was not symlinked into /opt/homebrew,
because macOS provides LibreSSL.


If you need to have openssl@3 first in your PATH, run:
  echo 'export PATH="/opt/homebrew/opt/openssl@3/bin:$PATH"' >> ~/.zshrc


For compilers to find openssl@3 you may need to set:
  export LDFLAGS="-L/opt/homebrew/opt/openssl@3/lib"
  export CPPFLAGS="-I/opt/homebrew/opt/openssl@3/include"


For pkg-config to find openssl@3 you may need to set:
  export PKG_CONFIG_PATH="/opt/homebrew/opt/openssl@3/lib/pkgconfig"
```


Check that everything works by running:


```
pkg-config --libs --cflags rdkafka


-I/opt/homebrew/Cellar/openssl@3/3.0.5/include -I/opt/homebrew/Cellar/librdkafka/1.9.2/include -I/opt/homebrew/Cellar/zstd/1.5.2/include -I/opt/homebrew/Cellar/lz4/1.9.4/include -L/opt/homebrew/Cellar/librdkafka/1.9.2/lib -lrdkafka
```


Now, all you need to do is run your tests with the `-tags dynamic` flag, it will instruct confluent-kafka-go to use the librdkafka library that we’ve built from source.


Thanks for reading and happy hacking!🫶


- \[1\] [https://hub.docker.com/r/confluentinc/cp-kafka/tags?page=1&name=arm64](https://hub.docker.com/r/confluentinc/cp-kafka/tags?page=1&name=arm64)