---
author: "Denis Nu\u021Biu"
categories:
- Python
- Reverse Engineering
- Security
date: '2019-08-21T20:45:48+00:00'
guid: https://nuculabs.wordpress.com/?p=473
id: 473
layout: post
permalink: /2019/08/21/introduction-to-angr/
tags:
- angr
- practical binary analysis
title: Introduction to Angr
---
I always wanted to play around with a binary analysis framework but most of the time I was turned off by how difficult it was to install and use it. Just recently I’ve thought to give angr a try and now I want to share my experience with you! I will present you a two scripts that solve two challenges, if you wish to dig deeper and learn Angr then you should visit it’s official documentation.


> angr is a python framework for analyzing binaries. It combines both static and dynamic symbolic (“concolic”) analysis, making it applicable to a variety of tasks.


For me the easiest way to install Angr and get it working on the first try was to download Kali linux, install it in VirtualBox (make sure you have at least 12 GB space for the disk) and execute: `pip install angr`.   
From here you can setup your Python dev environment in Kali as you please.


For the first challenge we have the following source code:


```
//written by bla
#include 
#include 
#include 






int main(int argc, char **argv)
{


        int count = atoi(argv[1]);
        int buf[10];


        if(count >= 10 ) 
                return 1;


        //printf("%lx\n", (size_t)(count * sizeof(int)));
        memcpy(buf, argv[2], count * sizeof(int));
        if(count == 0x574f4c46) {
        printf("WIN!\n");
                //execl("/bin/sh", "sh" ,NULL);
    } else
                printf("Not today son\n");




        return 0;
}




```


Challenge source: 
 level-7


The goal is to find two arguments to give to the program in order to overflow buf into count and display `WIN`. We can attempt to solve this with trial and error, debugging, do some computation or we can make Angr solve it for us with the following Python script.


```
import angr
import claripy


def resolve_win(state):
    # if the bytes of "WIN" are found in stdout it returns true
    return  b"WIN" in state.posix.dumps(1)


if __name__ == '__main__':
    print("starting.")


    # Declare project, load the binary
    proj = angr.Project('./lab-13/0-tutorial/level07')


    # Create a 32-bit symbolic bitvector named "password"
    arg1 = claripy.BVS('sym_arg', 8 * 11)  # maximum 11 * 8 bits
    arg2 = claripy.BVS('sym_arg', 8 * 44)  # maximum 44 * 8 bits


    # We construct an entry_state passing the two arguments
    st = proj.factory.entry_state(args=['./level07', arg1, arg2])
    # he st.libc.max_strtol_len tweak tells the atoi/strtol symbolic representation to
    # resolve strings that are of at most 11 bytes length (the default is 10)
    st.libc.max_strtol_len = 11


    # Now we will create what in angr terms is called a simulation manager.
    # https://docs.angr.io/core-concepts/pathgroups
    pg = proj.factory.simgr(st)


    # This can be read as: explore looking for the path p for which the current state
    # p.state contains the string "WIN" in its standard output (p.state.posix.dumps(1),
    # where 1 is the file descriptor for stdout).
    pg.explore(find=resolve_win)


    print("solution found")
    s = pg.found[0]
    print(s.posix.dumps(1)) # dump stdout


    # Print and eval the fist argument
    print("Arg1: ", s.solver.eval(arg1, cast_to=bytes))
    # Print and eval the second argument
    print("Arg2: ", s.solver.eval(arg2, cast_to=bytes))


```


Running the script will give us the solution for this binary, if the binary would change slightly (the count) we can still run the script and get a solution.


- - - - - -


The next challenge is easier, the binary is called multiple-styles and it can be downloaded from here: 


By looking at it’s disassembly output:


[![multiple-styles disassembly](https://res.cloudinary.com/practicaldev/image/fetch/s--eEIbd4ai--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fhAQi9A.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eEIbd4ai--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fhAQi9A.png)
We can see that the program does the following things:


1. Calls `read` which reads the ‘password’ from stdin into a buffer.
2. Loads the string “myvnvsuowsxs}ynk” into a buffer.
3. Loops through the buffer byte by byte adds 10 `00400a27 add dword [rbp-0x54 {var_5c_2} {var_5c_1}], 0xa` to it and compares it with the previously loaded string.
4. If they match it will jump to 0x00400a6c and print “you got it!”


At this point we can google for online caesar cipher, paste the string that got loaded and decipher it with an offset of -10, but we’re going to let angr `decipher` the password for us.


```
import angr
import claripy


if __name__ == '__main__':
    print("starting")
    proj = angr.Project("./multiple-styles", auto_load_libs=False)


    # Create a 32-bit symbolic bitvector named "password"
    password = claripy.BVS('password', 20*8)


    # We construct a blank_state with the address of main and we pass password to stdin
    st = proj.factory.blank_state(addr=0x004009ae, stdin=password)


    # We create a simulation manager
    pg = proj.factory.simulation_manager(st)


    # We tell angr to look for 0x00400a6c which is the starting address of the green block
    # that prints "you got it!" while telling him to avoid the address 0x00400a40
    pg.explore(find=(0x00400a6c), avoid=(0x00400a40))


    print("solution found")
    # We grab the solution.
    s = pg.found[0]


    # We can print the contents of stdin - 0:
    print("Flag: ", s.posix.dumps(0))


    # We can also get the password from our symbolic bitvector
    print("Pass: ", s.solver.eval(password, cast_to=bytes))
```


While writing the scripts I’ve used `angr` version `8.19.7.25. Please consult Angr’s official documentation if you wish to learn more!


Thank you for reading! 😀


References:


- 
 (challenge 1)
- 
- 
 (challenge 2)