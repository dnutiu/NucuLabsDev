---
author: "Denis Nu\u021Biu"
categories:
- Reverse Engineering
date: '2020-01-02T18:09:35+00:00'
guid: https://nuculabs.dev/?p=763
id: 763
layout: post
permalink: /2020/01/02/bypassing-ptrace-ld-preload/
tags:
- debugging
- LD_PRELOAD
- Linux
- ptrace
title: Bypassing ptrace calls with LD_PRELOAD on Linux
---
Hello,


Here’s a quick article on how to bypass calls to `ptrace` when debugging a Linux executable.


By calling `ptrace` with the `PTRACE_TRACEME` option, a process can detect if it’s being debugged and execute different instructions. This an effective anti-debugging technique.


For example, take the following C program:


```
int main() {
    if (ptrace(PTRACE_TRACEME, 0, 0, 0) < 0) {
      printf("I'm being debugged!n");
    } else {
      printf("Normal flown");
    }
    return 0;
}


```
If we execute the program from above we get `Normal flow` on our screen but if we debug it with `gdb` we get `Err: I'm being debugged!`


```
root@kali:~/Downloads# strace ./a.out 
...
munmap(0x7fb7c945e000, 90919)           = 0
ptrace(PTRACE_TRACEME)                  = -1 EPERM (Operation not permitted)
fstat(1, {st_mode=S_IFCHR|0620, st_rdev=makedev(0x88, 0), ...}) = 0
brk(NULL)                               = 0x55c2d37e6000
brk(0x55c2d3807000)                     = 0x55c2d3807000
write(1, "Err: I'm being debugged!n", 25Err: I'm being debugged!
) = 25
exit_group(0)                           = ?
+++ exited with 0 +++
```

To bypass this, we can use the `LD_PRELOAD` environment variable. It lets us control the loading path of a shared library, which allows us to stub out library functions such as `ptrace`.

We can create the following file:

```c
long ptrace(int request, int pid, void *addr, void *data) {
    return 0;
}

```
 And compile it as a shared library with the following command:

**gcc -shared ptrace.c -o ptrace.so**

Next, we can set the environment variable LD\_PRELOAD using the following commands:

- in the shell: `export LD_PRELOAD=./ptrace.so`
- in gdb: `set environment LD_PRELOAD=./ptrace.so`

Demo:

```
gdb-peda$ file a.out 
Reading symbols from a.out...
(No debugging symbols found in a.out)
gdb-peda$ r
Starting program: /root/Downloads/a.out 
Err: I'm being debugged!
[Inferior 1 (process 1939) exited normally]
Warning: not running
gdb-peda$ set environment LD_PRELOAD=./ptrace.so
gdb-peda$ r
Starting program: /root/Downloads/a.out 
Normal flow
[Inferior 1 (process 1946) exited normally]
Warning: not running
gdb-peda$ 
```


Thanks for reading!