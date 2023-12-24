---
title: "Implementing cat with Kotlin Native"
author: [ "Denis Nutiu" ]
date: '2023-12-24'
categories:
  - "Programming"
  - "Kotlin"
permalink: /2023/12/24/implementing-cat-with-kotlin-native
image: /hugo-content/2023-12/k-cat-cover.png
tags:
  - kotlin
  - macOs
  - linux
  - native
  - cat
  - tools
layout: post
---

![article cover image](/hugo-content/2023-12/k-cat-cover.png)

# Introduction

Hello! 👋

In this article we'll implement the `cat` command utility which is used in many Unix like systems like Linux and macOS.
To implement it, we'll use [kotlin](https://kotlinlang.org/) and to drop the Java Virtual Machine (JVM). We'll build
a binary directly with [Kotlin Native](https://kotlinlang.org/docs/native-overview.html). In the end, we'll get an 
executable that we can simply run on our Linux or macOS box.

# What is cat?

The `cat`utility which has purpose of concatenating files. You can use it by typing `cat file.txt` and it will output
the contents of the file into the terminal. It also has more options like: `usage: cat [-belnstuv] [file ...]` which 
are documented in the [manual pages](https://linux.die.net/man/1/cat).

# Kotlin & Kotlin Native

![article kotlin image](/hugo-content/2023-12/k-cat-kotlin.png)

Kotlin is a **modern** programming language that was first introduced by JetBrains in 2011 and later officially
supported by Google for Android development. It's designed to be fully interoperable with Java, while also 
fixing some of the common pain points of Java, making it more concise, expressive, and enjoyable to use.

Kotlin Native is a technology for compiling Kotlin code to native binaries, which can run without a virtual machine 
like the Java Virtual Machine (JVM). It is part of [Kotlin Multiplatform](https://kotlinlang.org/docs/multiplatform.html)
which is technology designed to simplify cross-platform development. You can use Kotlin Multiplatform to develop software
for Server, Android, iOS, Desktop and Web - JavaScript.

# Implementing cat with Kotlin Native

To follow along, I recommend that you use the [IntelliJ IDEA Community](https://www.jetbrains.com/idea/download/?section=mac) 
or [Professional](https://www.jetbrains.com/idea/) integrated development environment (IDE).

## Cloning the Kotlin Native template

Start by cloning the Kotlin Native project template, I'll come with everything preconfigured to get you started.

```
git clone https://github.com/Kotlin/kmp-native-wizard k-cat
```

Then open up the project in your IDE.

## Modifying the template

I've started modifying the template by first changing the project name to `k-cat`.

Open `.../IdeaProjects/k-cat/settings.gradle.kts` and set `rootProject.name`

```kotlin
rootProject.name = "k-cat"
```

And that's pretty much everything we need so far to give our project a proper name! 🔩

## Implementing cat

The `cat` utility is a complex program. We'll implement a simplified version that has the following requirements.

### Requirements

The program accepts a required string parameter which is implicit and represents the file path
and an optional `-n` flag parameter.

It will open the file given in the file path parameter, and it will print its contents on the terminal screen.

When called with the optional `-n` parameter it will print the line number before printing the file.

Example use:

```shell
./k-cat.kexe -n file.txt
0  Hello,
1  From nuculabs.dev 
```

### Implementation

To start implementing, please open: `.../IdeaProjects/k-cat/src/nativeMain/kotlin/Main.kt`.

#### Reading command line arguments

To read command line arguments, we need to add the `args: Array<String>` parameter to our main function. The args array
will contain command line arguments passed to the program.

```kotlin
fun main(args: Array<String>) {
 //...
}
```

#### Parsing command line arguments

To parse command line arguments, I've used simple Kotlin constructs.

The code is pretty much self-describing. First we search for the required filePath argument, and then we store its
value inside a variable. Secondly, we search for the optional `-n` shortform and `--numbering` longform argument, and
if it's found in the command line arguments array then we set the numbering boolean to true.

```kotlin
    // Grab the required file path argument
    val fileArgs = args.filterNot { it.startsWith("-") }
    if (fileArgs.size != 1) {
        println("Usage: missing path to file: ./k-cat <file>")
        return
    }
    val filePath = fileArgs.first()
    // Parse for optional `-n` argument
    var numbering = false;
    args.forEach {
        when (it) {
            "-n" -> numbering = true
            "--numbering" -> numbering = true
        }
    }
```

#### Reading Files

Reading files can be done by using already available function or by using a library. I've chosen the later one and
added the [okio](https://square.github.io/okio/) dependency to my project.

To add a dependency open `.../IdeaProjects/k-cat/build.gradle.kts` and change `sourceSets` as following:

```kotlin
kotlin {
    //...
    sourceSets {
        val nativeMain by getting
        val nativeTest by getting
        val commonMain by getting {
            dependencies {
                implementation("com.squareup.okio:okio:3.7.0")
            }
        }
    }
}
```

Then you can import and use the library in your main function to read files. For example:

```kotlin
import okio.FileSystem
import okio.Path.Companion.toPath
import okio.use
import okio.buffer

FileSystem.SYSTEM.source("file.txt".toPath()).use { fileSource ->
    fileSource.buffer().use { bufferedFileSource ->
        while (true) {
          val line = bufferedFileSource.readUtf8Line() ?: break
          // do something with line
        }
    }
}
```

#### Implementing simple k-cat 

The implementation is now straightforward, since we know how to parse command line arguments and to read files.

The following piece of code implements `k-cat`. It reads all the lines from a file path, and if numbering is true, it will
print the line number and the line, otherwise it will only print the line.

```kotlin
    FileSystem.SYSTEM.source(filePath.toPath()).use { fileSource ->
        fileSource.buffer().use { bufferedFileSource ->
            var number = 0
            while (true) {
                val line = bufferedFileSource.readUtf8Line() ?: break
                if (numbering) {
                    val formattedNumber = number.toString().padEnd(2, ' ')
                    println("$formattedNumber $line")
                } else {
                    println(line)
                }
                number += 1
            }
        }
    }
```

# Conclusion

In summary, Kotlin Native extends the capabilities of Kotlin, enabling developers to compile Kotlin code into native 
binaries for a variety of platforms, including desktop, web, android, iOS and embedded systems. 
This extension offers the performance of native applications while maintaining Kotlin's expressiveness and safety features. 

Thanks for reading! Happy Holidays! 🎄✨

### Further Reading / References

- https://kotlinlang.org/docs/native-get-started.html#build-and-run-the-application
- https://kotlinlang.org/docs/native-overview.html
- https://github.com/Kotlin/kmp-native-wizard

### Full Code: Main.kt

```kotlin
import okio.FileSystem
import okio.Path.Companion.toPath
import okio.use
import okio.buffer

fun main(args: Array<String>) {
    // Grab the required file path argument
    val fileArgs = args.filterNot { it.startsWith("-") }
    if (fileArgs.size != 1) {
        println("Usage: missing path to file: ./k-cat <file>")
        return
    }
    val filePath = fileArgs.first()
    // Parse for optional `-n` argument
    var numbering = false;
    args.forEach {
        when (it) {
            "-n" -> numbering = true
            "--numbering" -> numbering = true
        }
    }

    FileSystem.SYSTEM.source(filePath.toPath()).use { fileSource ->
        fileSource.buffer().use { bufferedFileSource ->
            var number = 0
            while (true) {
                val line = bufferedFileSource.readUtf8Line() ?: break
                if (numbering) {
                    val formattedNumber = number.toString().padEnd(2, ' ')
                    println("$formattedNumber $line")
                } else {
                    println(line)
                }

                number += 1
            }
        }
    }
}
```
