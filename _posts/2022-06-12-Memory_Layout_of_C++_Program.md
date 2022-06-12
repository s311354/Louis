---
layout: post
title: "Memory Layout of Program"
date: 2022-06-12
tags: [Programming, C_C_plus_plus, Embedded]
---

"In computing, a code segment, also known as a text segment or simply as text, is a portion of an object file or the corresponding section of the program's virtual address space that contains executable instructions. The term "segment" comes from the memory segment, which is a historical approach to memory management that has been succeeded by paging. When a program is stored in an object file, the code segment is a part of this file; when the loader places a program into memory so that it may be executed, various memory regions are allocated (in particular, as pages), corresponding to both the segments in the object files and to segments only needed at run time."

## Brief ##
When we declare a variable in program, C++ allocates space for that variable from one of several memory regions:  One region of memory is reserved for variable that persist throughout the lifetime of the program, such as constant. This information is called static data. One region of memory is reserved for allocating a new block of memory called a stack frame to hold its local variables. This information is called stack.  One region of memory is reserved for allocating memory dynamically. This space comes from a pool of memory called the heap. 

In this blog post, I would like to discuss the typical layout of a simple computer's program memory.

## Typical Code Segment ##
 The typical layout of a simple computer's program memory is with the <span style="background-color: #FFFF00">text, various data, and stack and heap sections.</span> 

<figure><center><img src="{{ site.baseurl }}/picture/memory_layout.png" width="30%"></center></figure>

+ **Text section**:  <span style="background-color: #FFFF00">contains executable instructions and is sharable </span> so that only a single copy needs to be in memory for frequently executed programs. It is often read-only and may be placed below the heap or stack in order to prevent heaps and stack overflows from overwriting it.

+ **Data section**: divided into two parts
    + Initialized Data Segment: <span style="background-color: #FFFF00">contains the global variables and static variables that are initialized by the programmer </span>. It is not read-only since the values of the variables can be altered at run time.

    + Uninitialized Data Segment:  <span style="background-color: #FFFF00">often called the “bss” segment and is initialized by the kernel to arithmetic 0 </span> before the program starts executing uninitialized data starts at the end of the data segment . It contains all global variables and static variables that are initialized to zero or do not have explicit initialization in source code.

+ **Stack section**: contains the program stack, a LIFO structure and <span style="background-color: #FFFF00">stores virtual pointer</span>. <span style="background-color: #FFFF00">Each time a recursive function calls itself, a new stack frame is used, so one set of variables doesn’t interfere with the variables from another instance of the function.</span> When the program tries to use more memory space than the call stack has available, it will occur a stack overflow.

+ **Heap section**: <span style="background-color: #FFFF00">managed by malloc, realloc, and free</span>, which may use the brk and sbrk system calls to adjust its size. (brk, sbrk – change data segment size). It is shared by all shared libraries and dynamically loaded modules in a process.

&lt;Remark&gt;
An industry group led by major Japanese central processing unit (CPU) manufacturers have addressed the <span style="background-color: #FFFF00">shortcomings of C++ for embedded applications</span>: While maximizing execution efficiency and making compiler construction simpler, the effort of programming is to preserve the most useful object-oriented features of the C++ language yet minimize code size.

## Examples of Memory Layout of objects (MAC OS) ##

1. Check the following simple program
<details markdown=block>
<summary markdown=span>*simple.c*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">int main(void)
{
    return 0;
}</span></code></pre></div></details>

Display summaries of the headers for each section:
```
$ objdump -h container 
container:      file format mach-o 64-bit x86-64
Sections:
Idx Name          Size     VMA              Type
  0 __text        0000000f 0000000100003fa0 TEXT
  1 __unwind_info 00000048 0000000100003fb0 DATA
```

2. Check the following simple program with one uninitialized global variable
<details markdown=block>
<summary markdown=span>*simple_uninitialized.c*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">int global; /* Uninitialized variable stored in bss*/
int main(void)
{
    return 0;
}</span></code></pre></div></details>

Display summaries of the headers for each section:
```
$ objdump -h container
container:      file format mach-o 64-bit x86-64
Sections:
Idx Name          Size     VMA              Type
  0 __text        0000000f 0000000100003fa0 TEXT
  1 __unwind_info 00000048 0000000100003fb0 DATA
  2 __common      00000004 0000000100004000 BSS
```

3. Check the following simple program with one uninitialized global variable and one uninitialized static variable
<details markdown=block>
<summary markdown=span>*simple_uninitialized_static.c*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">int global; /* Uninitialized variable stored in bss*/
int main(void)
{
    static int i; /* Uninitialized static variable stored in bss */
    return 0;
}</span></code></pre></div></details>

Display summaries of the headers for each section:
```
$ objdump -h container
container:      file format mach-o 64-bit x86-64
Sections:
Idx Name          Size     VMA              Type
  0 __text        0000000f 0000000100003fa0 TEXT
  1 __unwind_info 00000048 0000000100003fb0 DATA
  2 __common      00000004 0000000100004000 BSS
  3 __bss         00000004 0000000100004004 BSS
```
4. Check the following simple program with one initialized global variable and one uninitialized static variable
<details markdown=block>
<summary markdown=span>*simple_initialized.c*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">int global 10; /* Initialized variable stored in DATA*/
int main(void)
{
    static int i; /* Uninitialized static variable stored in bss */
    return 0;
}</span></code></pre></div></details>

Display summaries of the headers for each section:
```
$ objdump -h container
container:      file format mach-o 64-bit x86-64
Sections:
Idx Name          Size     VMA              Type
  0 __text        0000000f 0000000100003fa0 TEXT
  1 __unwind_info 00000048 0000000100003fb0 DATA
  2 __data        00000004 0000000100004000 DATA
  3 __bss         00000004 0000000100004004 BSS
```

5. Check the following simple program with one initialized global variable and one initialized static variable
<details markdown=block>
<summary markdown=span>*simple_initialized_static.c*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">int global = 10; /* Initialized variable stored in DATA*/
int main(void)
{
    static int i = 1; /* Initialized static variable stored in DATA */
    return 0;
}</span></code></pre></div></details>

Display summaries of the headers for each section:
```
$ objdump -h container
container:      file format mach-o 64-bit x86-64
Sections:
Idx Name          Size     VMA              Type
  0 __text        0000000f 0000000100003fa0 TEXT
  1 __unwind_info 00000048 0000000100003fb0 DATA
```

## Reference ##

+ [Wiki: Code segment](https://en.wikipedia.org/wiki/Code_segment)

+ [Wiki: Embedded C++](https://en.wikipedia.org/wiki/Embedded_C%2B%2B)

+ [Memory Layout in C++](https://medium.com/@vivekkr1020/memory-layout-in-c-87f8b8c67fc5)

+ [Memory Layout of C Programs](https://www.geeksforgeeks.org/memory-layout-of-c-program/)
