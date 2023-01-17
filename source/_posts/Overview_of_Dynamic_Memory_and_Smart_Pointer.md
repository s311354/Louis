---
title: Overview of Dynamic Memory and Smart Pointer
date: 2023-01-17
categories:
- louissrliu
- features
tags:
- programming
- cpp
toc: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/grassland.jpeg
---

Smart pointer object has a ability of taking ownership of pointer: once they take ownership they manage the pointed object by becoming responsible for its deletion at some point.

<!-- more -->

## Intorduction ##

The C++ language allows programmers to manually allocate/deallocate memory by static allocation or dynamic allocation. The static memory is allocated from the stack and used for local static objects, for class static data members and for variables defined outside any function. The dynamic memory is allocated from the free store or heap and its allocation with new and delete. However, dynamically allocated memory is right runtime, known as memory leak, or free the memory when there are still pointers referring to that memory. There is a way to avoid memory leak or manage dynamic allocation smartly by using smart pointer.

In this post, I would like to try going through the basic concept of dynamic memory management, smart pointer, std::unique_ptr, and how it contributes to avoid memory leak issue, definitely lost.

## Dynamic Memory Management ##

C++ dynamic memory management always follows the order of memory allocation, object construction, object destruction, and memory deallocation. A common way to create new object on dynamical memory is to use the [new][new] and [delete][delete] expression. Specifically, new allocates memory and constructs the object on memory, whereas delete destructs the object and releases the memory.

In fact, we should deallocate all dynamically allocated memory appropriately at the program exit. The reason is that it will lead to the memory leaks, which occur when we allocate heap memory (with new or malloc/calloc) but don't deallocate it.

For example, we created a linked list structure container which contains data and a pointer to the next dynamic structure container which contains data and a pointer to the next dynamic structure container ... and so on. And also implemented a function for adding two non-empty linked lists and returning the sum as a linked list.

{% codeblock %}
#include <cstdlib>
#include <memory>
#include <vector>

struct LinkedListNode {
    LinkedListNode() : val(0), next(nullptr) {}
    LinkedListNode(int x) : val(x), next(nullptr) {}
    LinkedListNode(int x, LinkedListNode* next) : val(x), next(next) {}
    virtual ~LinkedListNode() {};
    int val;
    LinkedListNode* next;
};

LinkedListNode* getNewHead(int value) {
    LinkedListNode* node ( new LinkedListNode(value, nullptr) );
    node->val = value;
    node->next = nullptr;
    return node;
}

LinkedListNode* insertLinkedlistNode(LinkedListNode* node, int value) {
    if (node == nullptr) {
        if (value != -1) node = getNewHead(value);
        return node;
    }
    node->next = insertLinkedlistNode(node->next, value);
    return node;
}

LinkedListNode* addTwoNumbers(LinkedListNode* l1, LinkedListNode* l2) {
    int sum = 0;
    LinkedListNode node (0);
    LinkedListNode* temp  = &node;
    while (sum || l1 || l2 ) {
        sum += (l1 ? l1->val : 0) + (l2 ? l2->val : 0);
        LinkedListNode* next_node = new LinkedListNode(sum%10);
        temp->next = next_node;
        temp = temp->next;
        sum /= 10;
        if (l1) l1 = l1->next;
        if (l2) l2 = l2->next;
    }
    return node.next;
}

int main () {
    std::vector<int> l1 = {2, 4, 3}, l2 = {5, 6, 4};
    std::vector<int> expected_value = {7, 0, 8};
    LinkedListNode* l1_node = nullptr;
    LinkedListNode* l2_node = nullptr;
    for (int i = 0; i < l1.size(); ++i) {
        l1_node = insertLinkedlistNode(l1_node, l1[i]);
        l2_node = insertLinkedlistNode(l2_node, l2[i]);
    }
    LinkedListNode* node = nullptr;
    node = addTwoNumbers(l1_node, l2_node);
    return 0;
}
{% endcodeblock %}

It is possible that calling the LinkedListNode object constructor will allocate while creating a new structure container. However, in this example, since we don't deallocate storage space previously allocated to it by a call to operator new and rendering that pointer location invalid, memory deallocation without destructor result in memory leak.

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 100%">Ubuntu16# valgrind  --leak-check=full ./dynamic_linkedlistnode.out
==11540== Memcheck, a memory error detector
==11540== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==11540== Using Valgrind-3.20.0 and LibVEX; rerun with -h for copyright info
==11540== Command: ./dynamic_linkedlistnode.out
==11540==
==11540==
==11540== HEAP SUMMARY:
==11540==     in use at exit: 19,052 bytes in 10 blocks
==11540==   total heap usage: 13 allocs, 3 frees, 19,088 bytes allocated
...
==11540== LEAK SUMMARY:
==11540==    definitely lost: 36 bytes in 3 blocks
==11540==    indirectly lost: 72 bytes in 6 blocks
==11540==      possibly lost: 0 bytes in 0 blocks
==11540==    still reachable: 18,944 bytes in 1 blocks
==11540==         suppressed: 0 bytes in 0 blocks
==11540== Reachable blocks (those to which a pointer was found) are not shown.
==11540== To see them, rerun with: --leak-check=full --show-leak-kinds=all
==11540==
==11540== For lists of detected and suppressed errors, rerun with: -s
==11540== ERROR SUMMARY: 3 errors from 3 contexts (suppressed: 0 from 0)</span></code></pre></div>

## Smart Pointer - std::unique_ptr ##

Since C++11, smart pointer is nice for when dynamic allocation is necessary. Since any dynamic memory requires a delete, it's much easier, much safer, and much less error prone to let the smart pointer take care of the cleanup, rather than doing it manually.

For example, in order to avoid returning dynamic structure container and memory leaks, we can manage object through a smart pointer.

{% codeblock %}
#include <cstdlib>
#include <iostream>
#include <memory>
#include <vector>

struct LinkedListNode
{
    LinkedListNode() : val(0), next(nullptr) {}
    LinkedListNode(int x) : val(x), next(nullptr) {}
    LinkedListNode(int x, std::unique_ptr<LinkedListNode> next) : val(x), next(std::move(next)) {}
    virtual ~LinkedListNode() {};
    int val;
    std::unique_ptr<LinkedListNode> next;
};

std::unique_ptr<LinkedListNode> getNewHead(int value)
{
    std::unique_ptr<LinkedListNode> node ( new LinkedListNode(value, nullptr) );
    return node;
}

std::unique_ptr<LinkedListNode> insertLinkedlistNode(std::unique_ptr<LinkedListNode> node, int value)
{
    if (node == nullptr) {
        if (value != -1) node = getNewHead(value);
        return node;
    }
    node->next = insertLinkedlistNode(std::move(node->next), value);
    return node;
}

std::unique_ptr<LinkedListNode> addTwoNumbers(std::unique_ptr<LinkedListNode> l1, std::unique_ptr<LinkedListNode> l2)
{
    int sum = 0;
    std::unique_ptr<LinkedListNode> node (new LinkedListNode(-1));
    std::unique_ptr<LinkedListNode> temp  = std::move(node);
    while (sum || l1 || l2 ) {
        sum += (l1 ? l1->val : 0) + (l2 ? l2->val : 0);
        temp = insertLinkedlistNode(std::move(temp), sum%10);
        sum /= 10;
        if (l1) l1 = std::move(l1->next);
        if (l2) l2 = std::move(l2->next);
    }
    node = std::move(temp->next);
    return node;
}

int main () {
    std::vector<int> l1 = {2, 4, 3}, l2 = {5, 6, 4};
    std::vector<int> expected_value = {7, 0, 8};
    std::unique_ptr<LinkedListNode> l1_node = nullptr;
    std::unique_ptr<LinkedListNode> l2_node = nullptr;
    for (int i = 0; i < l1.size(); ++i) {
        l1_node = insertLinkedlistNode(std::move(l1_node), l1[i]);
        l2_node = insertLinkedlistNode(std::move(l2_node), l2[i]);
    }
    std::unique_ptr<LinkedListNode> node(new LinkedListNode(0, nullptr));
    node = addTwoNumbers(std::move(l1_node), std::move(l2_node));
    return 0;
}
{% endcodeblock %}

Because the smart pointer object has a ability of taking ownership of pointer: once they take ownership they manage the pointed object by becoming responsible for its deletion at some point. That's why we can fix the memory leak in this case.

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 100%">Ubuntu16# valgrind  --leak-check=full ./smart_pointer_linkedlist.out
==11581== Memcheck, a memory error detector
==11581== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==11581== Using Valgrind-3.20.0 and LibVEX; rerun with -h for copyright info
==11581== Command: ./smart_pointer_linkedlist.out
==11581==
==11581==
==11581== HEAP SUMMARY:
==11581==     in use at exit: 18,944 bytes in 1 blocks
==11581==   total heap usage: 30 allocs, 29 frees, 20,328 bytes allocated
==11581==
==11581== LEAK SUMMARY:
==11581==    definitely lost: 0 bytes in 0 blocks
==11581==    indirectly lost: 0 bytes in 0 blocks
==11581==      possibly lost: 0 bytes in 0 blocks
==11581==    still reachable: 18,944 bytes in 1 blocks
==11581==         suppressed: 0 bytes in 0 blocks
==11581== Reachable blocks (those to which a pointer was found) are not shown.
==11581== To see them, rerun with: --leak-check=full --show-leak-kinds=all
==11581==
==11581== For lists of detected and suppressed errors, rerun with: -s
==11581== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)</span></code></pre></div>

## Reference ##

+ [Cppreference: smart pointers](https://en.cppreference.com/book/intro/smart_pointers)

[new]:https://en.cppreference.com/w/cpp/language/new "https://en.cppreference.com/w/cpp/language/new"

[delete]:https://en.cppreference.com/w/cpp/language/delete "https://en.cppreference.com/w/cpp/language/delete"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:shirong0419@icloud.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
