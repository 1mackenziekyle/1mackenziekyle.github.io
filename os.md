---
layout: page
title: OS161 
permalink: /os161-kernel-development/
---

# OS161 Operating System 
*Sept. 2024 - Present* 

I am taking the course CPEN 331 at UBC, an Operating Systems course, in which I am writing OS-level code to add to the educational OS/161 from Harvard's CS161 course. So far, I have implemented synchronization primitive types for multi-threaded programming, and implemented the filetable system, which manages the synchronized access to files between processes. 

#### Synchronization

I used synchronization primitives (namely spinlocks) to implement higher-level primitives to aid in writing concurrent multi-threaded code for a multi-core machine, including higher-level locks, semaphores, and condition variables.

These synchronization tools were then tested using unit tests that spawned hundreds of threads, and manipulated shared data safely.

#### Syscalls 

I implemented the syscalls that linux uses to modify files: `open()`, `close()`, `read()`, `write()`, and `lseek()`. These syscalls required the implementation of a system-wide data structure that tracks open and closed files, and manages the shared access to them between processes. We followed the standard Unix file system structure with a few small modifications.

![picture 1](media/filetable.png)  
*Standard Unix file table*




