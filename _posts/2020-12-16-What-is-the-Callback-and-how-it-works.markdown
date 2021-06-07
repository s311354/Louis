---
layout: post
title: What is the callback and how it works by C programming
date: 2020-12-16 00:32
tags: [C_C_plus_plus]
---

In computer programming, a callback function is any executable code that is passed an argument to other code; the other code is expected to call back (execute) the argument at the given time. Callbacks have a wide variety of uses. For example in error signaling, a Unix program might want to handle the termination properly when receiving SIGTERM and then would register the cleanup function as a callback. 

## Invoke Callback Function Used by C Programming ##

Here, I took a simple example of how to allow to register the callback function in C and then pass a content. Hope it would be helpful to understand. :)  

#### Example ####
<pre class="highlight">
callback_example.h
<code class="hljs"><span class="nb">typedef void (*callback_type) (void *msg, void *content, bool flag);

typedef struct _MyMsg {
    int appId; /*!< Description */
    char msgbody[32];
} MyMsg;

void notification_callback(void* msg, void* content, bool flag);

void register_notification(callback_type callback, void *msg, void *content, bool flag);

</span></code></pre>

<pre class="highlight">
callback_example.c<code class="hljs"><span class="nb">#include "callback_example.h"

void register_notification(callback_type callback, void *msg, void *content, bool flag) 
{
    callback(msg, content, flag);
}

void notification_callback(void* msg, void* content, bool flag)
{
    MyMsg *m_msg = (MyMsg *) msg;
    if (flag && m_msg != NULL) {
        m_msg->appId++;
    } else {
        assert(m_msg != NULL);
    }

    strcpy(m_msg->msgbody, content); 
}</span></code></pre>

<pre class="highlight">
main.c<code class="hljs"><span class="nb">#include "callback_example.h"

int main(void)
{
    MyMsg msg;
    msg.appId = 0;
    char message1[20] = "Hello World!\n";
    char message2[20] = "This is a test\n";

    register_notification((callback_type) notification_callback, &msg, message1, true); 

    printf("ID: %d, Message: %s\n", msg.appId, msg.msgbody);

    register_notification((callback_type) notification_callback, &msg, message2, true); 

    printf("ID: %d, Message: %s\n", msg.appId, msg.msgbody);

    return 0;
}
</span></code></pre>

<pre class="highlight">
Makefile<code class="hljs"><span class="nb">CC = gcc
CFLAGS = -g -Wall
ODIR = obj
SDIR = src

INC = -I .

OUT = exe/callback

callback: $(SDIR)
    $(CC) $(CFLAGS) $(INC) -c $(SDIR)/*.c -o $(ODIR)/*.o

main: main.c
    $(CC) $(CFLAGS) $(INC) -o $(OUT) main.c $(ODIR)/*o.

.PHONY: clean

clean:
    rm -f $(ODIR)/*.o $(OUT)
</span></code></pre>

#### The Expectation Result ####
<pre class="highlight"><code class="hljs"><span class="nb">$ ./exe/callback
ID: 1, Message: Hello World!

ID: 2, Message: This is a test</span></code></pre>

Now, we could observe that the calling function can pass whatever parameters it wishes to the called functions. The code that passes a callback to a calling function does not need to know the parameter values that will be passed to the function.


## Reference ##

[1] [Wiki Example](https://en.wikipedia.org/wiki/Callback_%28computer_programming%29)

[2] [Typedef for Function Pointers](https://riptutorial.com/c/example/31818/typedef-for-function-pointers)

[3] [Do C Callbacks Like This. Not Like That](https://mindtribe.com/2015/06/do-c-callbacks-like-this-not-like-that/)

[4] [POSTIX Timer](https://medium.com/vswe/posix-timer-1502348c2f9f)
