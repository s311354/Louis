---
title: How to Setup Gdb and Debug Program in Docker Container
date: 2023-02-09
categories:
- louissrliu
- features
tags:
- cpp
- gdb
- docker
toc: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/lake.jpeg
---

"The GNU Debugger (GDB) is a portable debugger that runs on many Unix-like systems and works for many programming languages, including C, C++, ... and partially others."

<!-- more -->

## Briefly ##

For software development, there are several usual debugging toolbox on Linux, such as [strace][strace], [perf][perf] and [gdb][gdb]. These are used to inspect behaviour of newly launched programs or of the already running ones. In this post, I would like to go through how to debug C/C++ programs for logic errors, segmentation faults and memory leaks on Docker container shortly.

## Docker Container ##

### Build and Run interact with Docker Image ###

To build the image with cmake and gdb
```
docker build . -t cpp-debug:0.0.1
```

To run and interact with above built image
```
docker run -it -v $(pwd)/examples:/mnt cpp-debug:0.0.1 bash
```

### Debug Examples in Docker Container ###

To build examples programs
```
$ mkdir build
$ cd build
$ cmake ..
$ make
```

#### Logical Error ####

When we run this logicalError program, which caculates the series value with following formula, we observated that the result did not return the expected value. First, we was well done indeed for using gdb to trace down problems.

```
$ ./logicalError 
This program is used to compute the value of the following series :
(x^0)/0! + (x^1)/1! + (x^2)/2! + (x^3)/3! + (x^4)/4! + ........ + (x^n)/n!
```

In gdb, at this point, enter the commands: break, run, next, step, print, and backtrace. It will look something like this:

```
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fgdb%2Fbugs%2F&data=05%7C01%7C%7Ce9e0213a90144c1ddbad08db0a65526b%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115202533025083%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=Subavd2vT%2Fo8zcWSni%2BlMco76RBwK%2FeydL49Lo8iDBY%3D&reserved=0>.
Find the GDB manual and other documentation resources online at:
<https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fgdb%2Fdocumentation%2F&data=05%7C01%7C%7Ce9e0213a90144c1ddbad08db0a65526b%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115202533025083%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=62EKXX2x7RQD3KrFX7ql9hW3p6%2B1t7ysSk0N2VYnLp8%3D&reserved=0>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from build/logicalError...done.
(gdb) b ComputeSeriesValue
Breakpoint 1 at 0xc4f: file /mnt/header.cpp, line 20.
(gdb) r
Starting program: /mnt/build/logicalError
warning: Error disabling address space randomization: Operation not permitted
This program is used to compute the value of the following series :
(x^0)/0! + (x^1)/1! + (x^2)/2! + (x^3)/3! + (x^4)/4! + .... + (x^n)/n!
Please enter the value of x : 2

Please enter an integer value for n :3


Breakpoint 1, ComputeSeriesValue (x=2, n=3) at /mnt/header.cpp:20
20          double seriesValue = 0.0;
(gdb) n
21          double xpow = 1;
(gdb)
23          for (int k = 0; k<=n; k ++) {
(gdb)
24              seriesValue += xpow / ComputeFactorial(k);
(gdb)
25              xpow = xpow * x;
(gdb)
23          for (int k = 0; k<=n; k ++) {
(gdb)
24              seriesValue += xpow / ComputeFactorial(k);
(gdb) step
ComputeFactorial (number=1) at /mnt/header.cpp:7
7           int fact = 0;
(gdb) print fact
$1 = 0
(gdb) n
12          for (int j = 1; j <= number; j ++) {
(gdb)
13              fact = fact * j;
(gdb)
12          for (int j = 1; j <= number; j ++) {
(gdb)
16          return fact;
(gdb) bt
#0  ComputeFactorial (number=1) at /mnt/header.cpp:16
#1  0x000055adafea6c7e in ComputeSeriesValue (x=2, n=3) at /mnt/header.cpp:24
#2  0x000055adafea6b4b in main () at /mnt/logicalError.cpp:26
```

By viewing the stack using backtrace, the root cause of unexpected value is pointed to the ComputeSeriesValue function. We can see the value of fact remains to be 0 before it is returned.

#### Segmentation Fault (core dumped) ####

When we run this coreDumped program, We can use gdb to trace segmentation fault step-by-step.

```
root@a2c941bd0212:/mnt# gdb build/coreDumped
GNU gdb (Ubuntu 8.1.1-0ubuntu1) 8.1.1
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fgnu.org%2Flicenses%2Fgpl.html&data=05%7C01%7C%7Cd30dce7b66124f88a92408db0a4df50e%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115102207439948%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=M1MI65ZgzOErPVwQosQYnV8c%2F0oZQ2exGqFQZIbVmCg%3D&reserved=0>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fgdb%2Fbugs%2F&data=05%7C01%7C%7Cd30dce7b66124f88a92408db0a4df50e%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115102207439948%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=MlRYNTm67tKwpHDOpil3knX6D8pMf47I2dq2RElUT0c%3D&reserved=0>.
Find the GDB manual and other documentation resources online at:
<https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fgdb%2Fdocumentation%2F&data=05%7C01%7C%7Cd30dce7b66124f88a92408db0a4df50e%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115102207439948%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=LRjiiVsGME0jH6Mc3WaBm%2B2iy3ZlEzGzBpUi3WfknLA%3D&reserved=0>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from build/coreDumped...done.
(gdb) r
Starting program: /mnt/build/coreDumped
warning: Error disabling address space randomization: Operation not permitted

Program received signal SIGSEGV, Segmentation fault.
0x00005598e28b166c in main () at /mnt/coreDumped.cpp:15
15          temp[3] = 'F';
(gdb) bt
#0  0x00005598e28b166c in main () at /mnt/coreDumped.cpp:15
```

By viewing the stack using backtrace, the root cause of segmentation fault is indicated in the line 15 of coreDumped.cpp.

Here's we can also get the stack for every thread with following command in gdb

```
root@a2c941bd0212:/mnt# gdb build/coreDumped
GNU gdb (Ubuntu 8.1.1-0ubuntu1) 8.1.1
Copyright (C) 2018 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fgnu.org%2Flicenses%2Fgpl.html&data=05%7C01%7C%7C0e6f594d56b5424c1a3608db0a5d5767%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115168250069764%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=m5pT5Xk7qABFXqzZlvRymdmE84Ch%2B7xEmDXxMWhtkRM%3D&reserved=0>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fgdb%2Fbugs%2F&data=05%7C01%7C%7C0e6f594d56b5424c1a3608db0a5d5767%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115168250225996%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=Tbppnhv8PByr%2FVyergRvhIQru2dMm7U53IJUZp%2B%2BVgY%3D&reserved=0>.
Find the GDB manual and other documentation resources online at:
<https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.gnu.org%2Fsoftware%2Fgdb%2Fdocumentation%2F&data=05%7C01%7C%7C0e6f594d56b5424c1a3608db0a5d5767%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638115168250225996%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=GFGVaYMO1d8T2dcSXJfJwJWRKoQuOtjaYLW2EO2oPQE%3D&reserved=0>.
For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from build/coreDumped...done.
(gdb) r
Starting program: /mnt/build/coreDumped
warning: Error disabling address space randomization: Operation not permitted

Program received signal SIGSEGV, Segmentation fault.
0x00005579a99a866c in main () at /mnt/coreDumped.cpp:15
15          temp[3] = 'F';
(gdb) thread apply all bt full

Thread 1 (process 209):
#0  0x00005579a99a866c in main () at /mnt/coreDumped.cpp:15
        temp = 0x5579a99a8734 "Paras"
        i = 0
```

By viewing the above stack using backtrace, the root cause of segmentation fault is also indicated in the line 15 of coreDumped.cpp and show every thread in gdb.

#### Memory Leak ####

When we run this memoryLeak program, gdb is not able to point out the memory leak issue. However, we don't need to do anything special to use valgrind, just call it for a build of program with an argument --leak-check=yes to see whether there are potential risks of memory leak, taken from [C++ Assignment Overloading and Valgrind Memcheck][valgrind].

```
root@a2c941bd0212:/mnt# valgrind --leak-check=yes build/memoryLeak
==206== Memcheck, a memory error detector
==206== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==206== Using Valgrind-3.13.0 and LibVEX; rerun with -h for copyright info
==206== Command: build/memoryLeak
==206==
==206==
==206== HEAP SUMMARY:
==206==     in use at exit: 12 bytes in 1 blocks
==206==   total heap usage: 2 allocs, 1 frees, 72,716 bytes allocated
==206==
==206== 12 bytes in 1 blocks are definitely lost in loss record 1 of 1
==206==    at 0x4C3289F: operator new[](unsigned long) (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)
==206==    by 0x10868B: main (memoryLeak.cpp:3)
==206==
==206== LEAK SUMMARY:
==206==    definitely lost: 12 bytes in 1 blocks
==206==    indirectly lost: 0 bytes in 0 blocks
==206==      possibly lost: 0 bytes in 0 blocks
==206==    still reachable: 0 bytes in 0 blocks
==206==         suppressed: 0 bytes in 0 blocks
==206==
==206== For counts of detected and suppressed errors, rerun with: -v
==206== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

By viewing the memory profile from valgrind, it identifies the bottlenecks of definitely lost in the line 3 of memoryLeak.cpp.

[strace]:https://en.wikipedia.org/wiki/Strace "https://en.wikipedia.org/wiki/Strace"

[perf]:https://perf.wiki.kernel.org/index.php/Main_Page "https://perf.wiki.kernel.org/index.php/Main_Page"

[gdb]:https://www.sourceware.org/gdb/ "https://www.sourceware.org/gdb/"

[valgrind]:https://louissrliu.github.io/2023/01/04/Cpp_assignment_overloading_and_valgrind_memcheck/ "https://louissrliu.github.io/2023/01/04/Cpp_assignment_overloading_and_valgrind_memcheck/"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:shirong0419@icloud.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
