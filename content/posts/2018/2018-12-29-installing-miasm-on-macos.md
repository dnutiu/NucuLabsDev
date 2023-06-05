---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Reverse Engineering
- Security
date: '2018-12-29T14:08:34+00:00'
guid: https://nuculabs.wordpress.com/?p=313
id: 313
layout: post
permalink: /2018/12/29/installing-miasm-on-macos/
tags:
- building miasm
- installing miasm
- miasm
- miasm2
title: Installing MIASM on macOS
---
I’ve been reading the [MIASM.re](http://www.miasm.re/blog/2016/01/26/welcome.html) blog recently and I wanted to give miasm a try. The scope of this article is to provide guidance on how to build miasm on macOS and introduce miasm to my readers.


> Miasm is a free and open source (GPLv2) reverse engineering framework. Miasm aims to analyze / modify / generate binary programs. Here is a non exhaustive list of features:
> 
> 
– https://github.com/cea-sec/miasm/


If you’re curious about miasm’s powers you should give these articles a quick read:


- [Fast DGA generation with Miasm](https://www.lexsi.com/securityhub/generation-rapide-de-dga-avec-miasm/?lang=en)
- [Deobfuscation: recovering an OLLVM-protected program](https://blog.quarkslab.com/deobfuscation-recovering-an-ollvm-protected-program.html)
- [Taming a wild nanomite-protected MIPS binary with symbolic execution: No Such Crackme](https://doar-e.github.io/blog/2014/10/11/taiming-a-wild-nanomite-protected-mips-binary-with-symbolic-execution-no-such-crackme/)


## Using MIASM with Docker


To execute the following command you must have [Docker](https://www.docker.com/) installed.


```
docker pull miasm/base
```


In order to get a running shell and mount the current working directory in the container you can use the following command:


```
docker run --rm -it --user root -v ${PWD}:/host miasm/base bash 
```


The working directory of the host machine is mounted in the container under **/host**. Running miasm scripts with python should be straight forward now.


One drawback to this approach is that the docker image was updated a year ago and it might not contain the latest functionality.


## Building MIASM os macOS


To build MIASM on macOS you will need Python 2.7.\* installed. If you don’t have it you can use [PyEnv](https://github.com/pyenv/pyenv) to install it. To build Python you need to install openssl from brew, link it and export the compiler variables. After getting that done you can proceed by installing elfesteem and other miasm dependencies.


```
git clone https://github.com/serpilliere/elfesteem.git elfesteem 
cd elfesteem 
python setup.py build 
python setup.py install
pip install pyparsing 
pip install pycparser 
```


Now clone miasm’s repo and prepare manually patch a header file.


```
git clone https://github.com/cea-sec/miasm.git miasm
cd miasm
# patch the file (details below)
python setup.py build
python setup.py install
```


If you build MIASM before the patch the build will fail with errors indicating the use of two undeclared identifiers: \_\_LITTLE\_ENDIAN and \_\_BIG\_ENDIAN. To fix this, edit the [miasm2/jitter/vm\_mngr.h](https://github.com/cea-sec/miasm/pull/438/commits/dc3d140b05047c310a2c94e3258bff6cc1ce81cb#diff-a3e30a539b92faa86ce46623d358ff2d) file as shown in the github link.


Building and installing miasm should work now. To check if it’s working try running a script from the examples directory.


```
(miasm) ➜  miasm git:(master) python example/disasm/full.py
 usage: Disassemble a binary [-h] [-m ARCHITECTURE] [-f] [-b BLOCKWATCHDOG]
                             [-n FUNCSWATCHDOG] [-r] [-v] [-g] [-z] [-l] [-s]
                             [-o SHIFTOFFSET] [-a] [-i] [-c] [-d] [-p] [-x]
                             [-y]
                             filename [address [address …]]
 Disassemble a binary: error: too few arguments
```


Next step is to add miasm’s jitter libraries to the path. Your miasm location and build folder name may be different:


```
xport DYLD_LIBRARY_PATH="~/miasm/miasm/build/lib.macosx-10.14-x86_64-2.7/miasm2/jitter:~/miasm/miasm/build/lib.macosx-10.14-x86_64-2.7/miasm2/jitter/arch"

```


Thanks for reading and happy holidays!