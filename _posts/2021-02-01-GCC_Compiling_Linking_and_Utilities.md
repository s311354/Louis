---
layout: post
title:   "GCC Compiling, Linking and Utilities"
date:    2021-01-31 12:48
tags:    [C_C_plus_plus]
---

If you got in touch with free software before, you might hear Richard Stallman, who is the founder of the GNU Project and original author of the GNU Compiler Collection (GCC). He founded the GNU project in 1984 to start Unix-like operation system project.

The original GNU Compiler Collection (GCC) has grown over times to support many languages, inculdes frond ends for C, C++, Objective-C. In addition, it is a key component of so-called "GNU Toolchian", for developing applications and writing operation systems. The GNU Toolchain includes GNU Compiler Collection, GNU Make, GNU Binutils, GNU Debugger(GDB), GNU Autotools and GNU Bison. 

Here, I took notes of the concepts and some basic topics in this post. Hope it would be helpful to understand and recap the knowledge. :)


<p align="center"><iframe src="https://giphy.com/embed/SXxI9NlwvYiY3bRsck" width="480" height="319" frameBorder="0" class="giphy-embed" allowFullScreen></iframe></p>
## GNU C Compiler (GCC) ##

GCC has been ported to a wide variety of platforms and instruction set architecture and is wide deployed as a tool in the development of both free and properiety software. GCC is also available for many embedded systems, including ARM-based and Power ISA-based chips.

For my MacOS, the GCC version is shown below. You could see that it is using GCC to compil 64-bit code and bringing code to 64-bit target architecture. 

<pre class="highlight"><code class="hljs"><span class="nb">$ gcc -v
Configured with: --prefix=/Library/Developer/CommandLineTools/usr --with-gxx-include-dir=/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk/usr/include/c++/4.2.1
Apple clang version 12.0.0 (clang-1200.0.32.27)
Target: x86_64-apple-darwin20.1.0
Thread model: posix
InstalledDir: /Library/Developer/CommandLineTools/usr/bin
</span></code></pre>


### Compiler and Link Separately ###

A few commonly-used GCC compiler options are:
- -o: specifies the output executable filename
- -Wall: prints "all" Warning messages
- -g: generate additional symbolic debugging information for use with gdb debugger

<pre class="highlight">For example:<code class="hljs"><span class="nb">CC = gcc
CFLAGS = -g -Wall
DIR = obj
SDIR = src

INC = -I .

OUT = exe/timer

posix_timer:
	$(CC) $(CFLAGS) $(INC) -c $(SDIR)/$@.c -o $(DIR)/$@.o

main:
	$(CC) $(CFLAGS) $(INC) -o $(OUT) $@.c $(DIR)/*o

clean:
	rm -f $(DIR)/*.o $(OUT) core.*

mrproper: clean
	rm -f posix_timer
</span></code></pre>

### GCC Environment Variables ###

GCC uses the following environment variables:
- PATH: searching the executables and runtime shared libraries
- LIBRARY_PATH: searching library-paths for link libraries. It is searched after the paths specified in -L\<dir\> options


## Utilities for Examining the Compiled Files ##

For all the GNU utilities, you can use "man" command to display the man pages.

### file Utility ###

The utility "file" can be used to display the type of object files and executable files.

<pre class="highlight">For example:<code class="hljs"><span class="nb">$ file src/posix_timer.c
src/posix_timer.c: c program text, ASCII text 

$ file obj/posix_timer.o
obj/posix_timer.o: Mach-O 64-bit object x86_64

$ file exe/timer
exe/timer: Mach-O 64-bit executable x86_64
</span></code></pre>


### nm Utility ###

The utility "nm" lists symbol table of object files.

<pre class="highlight">For example:<code class="hljs"><span class="nb">$ nm obj/posix_timer.o
0000000000000110 T _hello_world
                 U _printf
                 U _pthread_cancel
                 U _pthread_self
00000000000000a0 T _should_kill_thread
                 U _sleep
                 U _time
</span></code></pre>

"nm" is commonly-used to check if a particular function is defined in an object file. The common symbol type are:
- T: indicates a function that is defined
- U: indicates a function which is undefined and should be resolved by the linker

### otool Utility ###

The utility "otool" is for MacOS system, its functionality is similar as ldd, and examines an executable to display a list of the shared libraries that it needs.

<pre class="highlight"><code class="hljs"><span class="nb">$ otool -L exe/timer
exe/timer:
        /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1292.0.0)
</span></code></pre>


## Reference ##
[GCC and Make Compiling, Linking and Building C/C++ Applications](https://www3.ntu.edu.sg/home/ehchua/programming/cpp/gcc_make.html#zz-2.3)

[Wiki - Richard Stallman](https://en.wikipedia.org/wiki/Richard_Stallman)

[Compiling 64-Bit Code](https://developer.apple.com/library/archive/documentation/Darwin/Conceptual/64bitPorting/building/building.html)
