---
title: How to Setup Environment Variable and Run Boost Thread Program in Docker Container
date: 2023-02-10
categories:
- louissrliu
- features
tags:
- cpp
- boost
- docker
toc: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/alviso.jpeg
---

"Boost.Thread enables the use of multiple threads of execution with shared data in portable C++ code. It provides classes and functions for managing the threads themselves, along with others for synchronizing data between the threads or providing separate copies of data specific to individual threads."

<!-- more -->

## Briefly ##

In concurrent computing, it can perform multiple tasks simultaneously. Boost.Thread is a famous library in the Boost C++ libraries that provides a platform-independent interface to threading and synchronization facilities. It provides a wide range of facilities for managing threads.

In this post, I would try passing through the modeling a bank account class that supports simultaneously deposits and withdrawals from multiple locations. Also, setup libboost_thread.so.1.80.0 environment in a Docker container to build boost concurrent execution of a component.

## Boost Thread Docker Container ##

The Dockfile and the Boost.Thread CMake are available on [boost threads example][threads].

Here is the CMakeLists.txt file allows us to build Boost.Thread execution.

```
cmake_minimum_required(VERSION 3.0.0)

project(BankAccount VERSION 0.0.1 LANGUAGES CXX)

find_package(Boost REQUIRED COMPONENTS thread)

set(CMAKE_CXX_STANDARD 14)

include_directories(${Boost_INCLUDE_DIRS})
add_executable(${PROJECT_NAME} bankaccount.cpp)
target_link_libraries(BankAccount Boost::thread)
```

### Build Boost Thread Example ###

This is an example of moduling a bank account class that supports simultaneously deposits and withdrawals from multiple locations object implementation.

```
BankAccount JoesAccount;

void bankAgent()
{
    for (int i = 10; i > 0; i --) {
        JoesAccount.Deposit(500);
    }
}

void Joe() {
    for (int i = 10; i > 0; --i) {
       // error: void value not ignored as it ought to be
       // int mypocket = JoesAccount.Withdraw(100);

       // The correct way
       JoesAccount.Withdraw(100);
       int mypocket = JoesAccount.GetBalance();
       std::cout << mypocket << std::endl;
    }
}

int main() {
    boost::thread thread1(bankAgent); // start concurrent execution of bankAgent
    boost::thread thread2(Joe);       // start concurrent execution of Joe
    thread1.join();
    thread2.join();
    return 0;
}
```

To build the boost thread example using CMake, we can run the following command.

```
$ cmake -B build
$ cmake --build build
```

### View the Stack for Every Threads ###

By viewing the stack for every threads, it shows how the balance in bank account changes from time to time.

Here we set the breakpints where bankAgent deposits and where Joe withdraws from bank account.
```
root@0518fede6045:/mnt# gdb build/boost_threads/BankAccount
GNU gdb (Ubuntu 7.11.1-0ubuntu1~16.5) 7.11.1
Copyright (C) 2016 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://aus01.safelinks.protection.outlook.com/?url=http%3A%2F%2Fgnu.org%2Flicenses%2Fgpl.html&data=05%7C01%7C%7C05bc8a5436354e8ec9a508db0d8577e3%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C638118639120585581%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&sdata=w1lRuyMBTUmuRsKkUaKnlOw2TeVmIAraujdPQmCsuzA%3D&reserved=0>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
...

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from build/boost_threads/BankAccount...(no debugging symbols found)...done.
(gdb) b BankAccount::Deposit
Breakpoint 1 at 0x41249e
(gdb) b BankAccount::Withdraw
Breakpoint 2 at 0x4124dc
...
```

Here we can type thread apply all backtrace full, which display the backtrace for all the threads; this is helpful for observing a core dump of a multi-threaded program.

```
(gdb) r
Starting program: /mnt/build/boost_threads/BankAccount
warning: Error disabling address space randomization: Operation not permitted
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
[New Thread 0x7f5140c35700 (LWP 18)]
[New Thread 0x7f5140434700 (LWP 19)]
[Switching to Thread 0x7f5140c35700 (LWP 18)]

Thread 2 "BankAccount" hit Breakpoint 1, 0x000000000041249e in BankAccount::Deposit(int) ()
(gdb) n
Single stepping until exit from function _ZN11BankAccount7DepositEi,
which has no line number information.
[Switching to Thread 0x7f5140434700 (LWP 19)]

Thread 3 "BankAccount" hit Breakpoint 2, 0x00000000004124dc in BankAccount::Withdraw(int) ()
(gdb)
Single stepping until exit from function _ZN11BankAccount8WithdrawEi,
which has no line number information.
[Switching to Thread 0x7f5140c35700 (LWP 18)]

Thread 2 "BankAccount" hit Breakpoint 1, 0x000000000041249e in BankAccount::Deposit(int) ()
(gdb)
Single stepping until exit from function _ZN11BankAccount7DepositEi,
which has no line number information.
400
[Switching to Thread 0x7f5140434700 (LWP 19)]

Thread 3 "BankAccount" hit Breakpoint 2, 0x00000000004124dc in BankAccount::Withdraw(int) ()
(gdb) thread apply all bt full

Thread 3 (Thread 0x7f5140434700 (LWP 19)):
#0  0x00000000004124dc in BankAccount::Withdraw(int) ()
No symbol table info available.
#1  0x000000000040f807 in Joe() ()
No symbol table info available.
#2  0x0000000000415301 in boost::detail::thread_data<void (*)()>::run() ()
No symbol table info available.
#3  0x00007f5141ac7075 in thread_proxy () from /usr/local/lib/libboost_thread.so.1.80.0
No symbol table info available.
#4  0x00007f51418a86ba in start_thread (arg=0x7f5140434700) at pthread_create.c:333
        __res = <optimized out>
        pd = 0x7f5140434700
        now = <optimized out>
        unwind_buf = {cancel_jmp_buf = {{jmp_buf = {139986947229440, 6099298229468553489, 0, 140737064342143, 139986947230144, 0, -6194824523399536367,
                -6194828308583128815}, mask_was_saved = 0}}, priv = {pad = {0x0, 0x0, 0x0, 0x0}, data = {prev = 0x0, cleanup = 0x0, canceltype = 0}}}
        not_first_call = <optimized out>
        pagesize_m1 = <optimized out>
        sp = <optimized out>
        freesize = <optimized out>
        __PRETTY_FUNCTION__ = "start_thread"
#5  0x00007f514104651d in clone () at ../sysdeps/unix/sysv/linux/x86_64/clone.S:109
No locals.

Thread 2 (Thread 0x7f5140c35700 (LWP 18)):
#0  0x00000000004124a2 in BankAccount::Deposit(int) ()
No symbol table info available.
#1  0x000000000040f7da in bankAgent() ()
No symbol table info available.
#2  0x0000000000415301 in boost::detail::thread_data<void (*)()>::run() ()
No symbol table info available.
#3  0x00007f5141ac7075 in thread_proxy () from /usr/local/lib/libboost_thread.so.1.80.0
No symbol table info available.
#4  0x00007f51418a86ba in start_thread (arg=0x7f5140c35700) at pthread_create.c:333
        __res = <optimized out>
        pd = 0x7f5140c35700
        now = <optimized out>
        unwind_buf = {cancel_jmp_buf = {{jmp_buf = {139986955622144, 6099298229468553489, 0, 140737064342143, 139986955622848, 0, -6194825626669260527,
                -6194828308583128815}, mask_was_saved = 0}}, priv = {pad = {0x0, 0x0, 0x0, 0x0}, data = {prev = 0x0, cleanup = 0x0, canceltype = 0}}}
        not_first_call = <optimized out>
        pagesize_m1 = <optimized out>
        sp = <optimized out>
        freesize = <optimized out>
        __PRETTY_FUNCTION__ = "start_thread"
#5  0x00007f514104651d in clone () at ../sysdeps/unix/sysv/linux/x86_64/clone.S:109
No locals.

Thread 1 (Thread 0x7f5141ef2740 (LWP 14)):
#0  pthread_cond_wait@@GLIBC_2.3.2 () at ../sysdeps/unix/sysv/linux/x86_64/pthread_cond_wait.S:185
No locals.
#1  0x00007f5141accd4b in boost::condition_variable::wait(boost::unique_lock<boost::mutex>&) () from /usr/local/lib/libboost_thread.so.1.80.0
No symbol table info available.
#2  0x00007f5141ac71c4 in boost::thread::join_noexcept() () from /usr/local/lib/libboost_thread.so.1.80.0
No symbol table info available.
#3  0x0000000000412220 in boost::thread::join() ()
---Type <return> to continue, or q <return> to quit---
No symbol table info available.
#4  0x000000000040f87f in main ()
No symbol table info available.
...
```

So from running the code, this stack shows that the bankAgent and Joe are executed concurrently.

```
...
Thread 2 "BankAccount" hit Breakpoint 1, 0x000000000041249e in BankAccount::Deposit(int) ()
(gdb)
Single stepping until exit from function _ZN11BankAccount7DepositEi,
which has no line number information.
3200
[Switching to Thread 0x7f5140434700 (LWP 19)]

Thread 3 "BankAccount" hit Breakpoint 2, 0x00000000004124dc in BankAccount::Withdraw(int) ()
(gdb)
Single stepping until exit from function _ZN11BankAccount8WithdrawEi,
which has no line number information.
[Switching to Thread 0x7f5140c35700 (LWP 18)]

Thread 2 "BankAccount" hit Breakpoint 1, 0x000000000041249e in BankAccount::Deposit(int) ()
(gdb)
Single stepping until exit from function _ZN11BankAccount7DepositEi,
which has no line number information.
3600
[Switching to Thread 0x7f5140434700 (LWP 19)]

Thread 3 "BankAccount" hit Breakpoint 2, 0x00000000004124dc in BankAccount::Withdraw(int) ()
(gdb)
Single stepping until exit from function _ZN11BankAccount8WithdrawEi,
which has no line number information.
[Thread 0x7f5140c35700 (LWP 18) exited]
0x000000000040f807 in Joe() ()
(gdb)
Single stepping until exit from function _Z3Joev,
which has no line number information.
4000
0x0000000000415301 in boost::detail::thread_data<void (*)()>::run() ()
(gdb)
...
```

## Reference ##

+ [Boost Synchronization](https://www.boost.org/doc/libs/1_81_0/doc/html/thread/synchronization.html)

+ [Openai Chat](https://chat.openai.com/chat)

[threads]:https://github.com/s311354/boost-docker/tree/main/examples/boost_threads "https://github.com/s311354/boost-docker/tree/main/examples/boost_threads"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:shirong0419@icloud.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
