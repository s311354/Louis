---
title: C++ Assignment Overloading and Valgrind Memcheck
date: 2023-01-04
categories:
- louissrliu
- features
tags:
- programming
- cpp
toc: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/flight.jpeg
---

"Valgrind is an instrumentation framework for building dynamic analysis tools. It comes with a set of tools each of which performs some kind of debugging, profiling, or similar task that helps you improve your programs."... from Wiki page.

<!-- more -->

## Introduction ##

Valgrind tools supports to automatically detect memory issues and threading bugs, and show the profile of programs in detail. Some of the most difficult C/C++ bugs come from mismanagement of memory: allocating the wrong size, using an uninitialized pointer, accessing memory at the misstep in procedure, overrunging a buffer, and so on. These types of errors are tricky, as they can provide little debugging information, such as "Segmentation fault", and tracking the observed problem back to underlying root.

## Memcheck ##

Memcheck is the default tool on the Valgrind command line, specifes "--tool=memcheck", a memory detector. It assists us to detect the memory issues that are common in C/C++ programs. There are two types of issues on memory errors and memory leaks.

### Memory Errors ###

The memory error is a red alert and should never be treated casually or ignored. We should keep in mind that errors are the primary concern. The most common memory errors are:

+ Accessing memory you shouldn't, e.g. overrunging and underrunning heap blocks, overrunging the top of the stack, and accessing memory after it has been freed.

+ Using undefined values, i.e. values that have not been initialised, or that have derived from other undefined values.

+ Incorrect freeing of heap memory, such as double-freeing blocks, or mismatched use of malloc/new/new[] versus free/delete/delete[].

+ Overlapping src and dst points in memcpy and related function

+ Passing a fishy (presumably negative) value to the size parameter of memory allocation function.

### Memory Leaks ###

The memory leaks occur when you allocate heap memory (with new or malloc/calloc) but don't free it. Valgrind categories leaks using these terms:

+ **definitely lost**: this means that no pointer to the block can be found. The block is classidied as "lost", because the programmer could not possibly have freed it at program exit, since no pointer to it exists. This is likely a symptom of having lost the pointer at some earlier point in the program. Such cases should be fixed!

+ **indirectly lost**: this means that the block is lost, not because there no pointers to it, but rather because all the blocks that point to it are themselves lost. The problem will disappear if the definitely lost blcok that point to it are themselves lost.

+ **possibly lost**: this means that a chain of one or more pointers to the block has been found, but at least one of the pointers is an interior-pointer. This could just be a random value in memory that happens to point into a block.

+ **still reachable**: a start-pointer or chain of start-pointers to the block is found. Since the block is still pointed at, you could have freed it before program exit.

### Practice An Exception Safe Assignment Operator ###

In the following practice, we strictly follow allocate, populate and deallocate in the copy assignment operator to avoid heap blocks were not freed error.

```
#include <cstddef>
#include <cstring>
#include <iostream>

class String
{
public:
    String(const char* s = "") : String(s, std::strlen(s) + 1) {}
    String(const String& other) : String(other.cstring) {}

    ~String() { delete [] cstring; }

    // Copy Assignment Operator
    String& operator=(const String& other)
    {
        if (this == &other) return *this;

        // non-static data member with default member initializer
        std::size_t n { std::strlen(other.cstring) + 1 };
        char * new_cstring = new char[n];           // allocate
        std::memcpy(new_cstring, other.cstring, n); // populate array
        delete[] cstring;                           // deallocate
        cstring = new_cstring;
        return *this;
    }

    // Comparison operators
    bool operator==(const String& other) const
    {
        if (std::strlen(cstring) != std::strlen(other.cstring)) return false;
        for (std::size_t i = 0; i < std::strlen(cstring); ++i)
        {
            if (cstring[i] != other.cstring[i]) return false;
        }
        return true;
    }

    operator const char *() const { return cstring; }

private:
    String(const char* s, std::size_t n) : cstring(new char[n])
    {
        std::memcpy(cstring, s, n);
    }
    char * cstring;
};

int main()
{
    String str_1{"abcd"};
    String str_2{"def"};
    str_1 = str_2;
}
```

Here is using Valgrind to check memory usage:
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">louis@louis-VirtualBox:~/GitHub/Exeception_Saft_assignment$ valgrind --leak-check=full ./a.out
==1186914== Memcheck, a memory error detector
==1186914== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==1186914== Using Valgrind-3.18.1 and LibVEX; rerun with -h for copyright info
==1186914== Command: ./a.out
==1186914==
==1186914==
==1186914== HEAP SUMMARY:
==1186914==     in use at exit: 0 bytes in 0 blocks
==1186914==   total heap usage: 4 allocs, 4 frees, 72,717 bytes allocated
==1186914==
==1186914== All heap blocks were freed -- no leaks are possible
==1186914==
==1186914== For lists of detected and suppressed errors, rerun with: -s
==1186914== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)</span></code></pre></div>

## Reference ##

+ [Valgrind Developers](https://valgrind.org/)

