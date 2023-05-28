---
author: "Denis Nu\u021Biu"
categories:
- Programming
date: '2018-07-27T19:39:03+00:00'
guid: https://nuculabs.wordpress.com/?p=163
id: 163
layout: post
permalink: /2018/07/27/introduction-to-gccs-extended-asm-format/
publicize_twitter_user:
- denisnutiu
- denisnutiu
- denisnutiu
- denisnutiu
tags:
- assembly
- gcc
- Linux
title: Introduction to GCC&#8217;s Extended ASM Format
---
As I’ve been reading Professional Assembly Language, I’ve come across chapter 13 and I liked it so much that I’m going to write a blog post about it. The book is quite nice if you’re interested in assembly for Linux.


Extended ASM format let’s you write assembly code that interacts with any type of C data, such as local variables, strings, numbers, goto labels and so on. The format produces cleaner, safer and more efficient code than the Basic ASM format.


There are two situations when Extended ASM cannot be used and Basic ASM is prefered:


- Extended ASM has to be inside a function, you cannot use it globally.


- Functions that are declared with the *naked* attribute also need to use Extended ASM. Functions that declared with naked attribute also need to setup their own prolog and epilogue


The template for Extended ASM is as follows:


\[code langage=”asm”\]  
asm \[volatile\]  
(  
 “assembly code\\n\\t”  
 “more assembly code\\n\\t”  
 : OutputOperands  
 : InputOperands  
 : ChangedRegisters  
)  
\[/code\]


OutputOperands and InputOperands are pretty clear what represent.  
ChangedRegisters represent registers that you used in your code, you’ll need to write them to tell the compiler not to touch them.


If you need to execute jumps to C declared goto labels then you need to specify the **goto** keyword and **: GotoLabels**.


\[code langage=”asm”\]  
asm \[volatile\] goto  
(  
“assembly code\\n\\t”  
“more assembly code\\n\\t”  
: OutputOperands  
: InputOperands  
: ChangedRegisters  
: GotoLabels  
)  
\[/code\]


The GCC’s compiler might optimize your ASM and remove it completely. To tell the compiler not to touch it you need to use the **volatile** keyword. More information can be found here: [Extended-Asm.html#Volatile](https://gcc.gnu.org/onlinedocs/gcc/Extended-Asm.html#Volatile)


Now that you’ve had a fast introduction to the topic, let’s explore these concepts with a few code examples. I have included some comments in the code to make it easier.


Some simple mathematical operation would be coded like this:


\[code language=”c”\]  
\#include stdio.h // brackets are missing thanks to wordpress’ shitty parser implementation


int main()  
{  
 int a = 0;  
 int b = 0;  
 int sum;


 asm  
 (  
 “movl $10, %%ecx\\n\\t” // Moves 10 to ECX (variable a)  
 “movl $15, %%edx\\n\\t” // Moves 15 to EDX (variable b)  
 “add %%edx, %%ecx\\n\\t” // ECX = ECX + EDX  
 “movl %%ecx, %%eax\\n\\t” // EAX = ECX ; EAX (variable sum)  
 : “=a”(sum) // OutputOperand: ‘=’ modifier (see docs) ;  
 : “d”(a), “c”(b) // InputOperands: ‘d’ (variable a) ; ‘c’ (variable b) See MachineConstraints for x86  
 : “%eax”, “%edx”, “%ecx”  
);


 // To understand that the constraint ‘a’ EAX and ‘d’ to EDX see MachineConstraints for the x86 family.


 printf(“The sum of a + b is %d\\n”, sum);  
 return 0;  
}  
\[/code\]


To understand what “=a” means see the documentation for Simple Constraints and for Machine Constraints (for the x86 family).


- ‘=’ is a constraint modified and it means that the operand is written to by the instruction.
- ‘a’ is a machine constraint that tells the compiler “Put the variable sum into the EAX register”


The same example from above could be written using asmSymbolicName syntax, which makes the code more readable and maintainable.


\[code language=”c”\]  
\#include stdio.h


int main()  
{  
 int a = 0;  
 int b = 0;  
 int sum;


 asm  
 (  
 “movl $10, %\[OpA\]\\n\\t”  
 “movl $15, %\[OpB\]\\n\\t”  
 “add %\[OpA\], %\[OpB\]\\n\\t”  
 “movl %\[OpB\], %\[Sum\]\\n\\t”  
 : \[Sum\] “=r”(sum)  
 : \[OpA\] “r”(a), \[OpB\] “r”(b)  
 );


 printf(“The sum of a + b is %d\\n”, sum);  
 return 0;  
}  
\[/code\]


Let’s move on to the next program. A simple string copy would look like this:


\[code language=”c”\]  
\#include  
\#include  
int main()  
{  
 unsigned length = 13;  
 char \* src = “ASM is great!”;  
 char \* dest = (char \*) malloc(length \* sizeof(char));


 asm volatile  
 (  
 “cld\\n\\t” // Clear direction flag.  
 “repz movsb\\n\\t” // Repeat MOVSB until ECX reaches zero.  
 : // OutputOperand  
 : “D”(dest), “S”(src), “c”(length) // InputOperands:  
 );


 printf(“Input: %s\\n”, src);  
 printf(“Output: %s\\n”, dest);


 return 0;  
}  
\[/code\]


After some tries I managed to write a more complicated example where you’d want to set a character in a string at the position X while checking for bounds. The final code looks like this:


https://gist.github.com/dnutiu/282c063824936f40b1156c9a57233d67


I hope you enjoyed this article 🙂


Thank you for reading, happy hacking!


### Resources:


- https://gcc.gnu.org/onlinedocs/gcc/Extended-Asm.html
- https://gcc.gnu.org/onlinedocs/gcc/Basic-Asm.html
- https://gcc.gnu.org/onlinedocs/gcc/Modifiers.html#Modifiers
- https://gcc.gnu.org/onlinedocs/gcc/Simple-Constraints.html#Simple-Constraints
- https://gcc.gnu.org/onlinedocs/gcc-6.2.0/gcc/Machine-Constraints.html#Machine-Constraints (See x86 family)