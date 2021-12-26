---
layout: post
title:   "Dynamic Memory and Smart Pointer unique_ptr"
date:    2021-12-22
tags:    [C_C_plus_plus]
---

## Abstract ##
The C++ language allows programmers to manually allocate/decallocate memory by static allocation or dynamic allocation. The static memory is allocated from the stack and used for local static objects, for class static data members and for variables defined outside any function. The dynamic memory is allocated from the free store or heap and its allocation with new and delete. However, dynamically allocated memory is notoriously tricky to manage correctly because it is surprisingly hard to ensure to either free memory at the right run1time, known as memory leak, or free the memory when there are still pointers referring to that memory. There is a way to avoid memory leak or manage dynamic allocation smartly by using smart pointer - unique_ptr. In this post, I would like to disscuss the basic concept of smart pointer unique_ptr.

## Smart Pointers - unique_ptr ##
Smart pointers unique_ptr is nice for when dynamic allocation is necessary. Since any dynamic memory requires a delete, it's much easier, much safer, and much less error prone to let the smart pointer take care of the cleanup, rather than doing it manually.

Thr uses of smart pointer unique_ptr include providing exception safety for dynamically allocated memory, passing ownership of dynamically allocated memory to a function, and returning dynamically allocated memory from a function. The unique_ptr implementation could be referred from C++ STL library or boost C++ library.

<div class="language-shell highlighter-rouge"><pre class="highlight">Boost C++ library<code class="hljs ruby"><span class="nb">// /usr/local/Cellar/boost/1.76.0/include/boost/move/unique_ptr.hpp
...
namespace movelib {
...
//! \tparam T Provides the type of the stored pointer.
//! \tparam D The deleter type:
//!   -  The default type for the template parameter D is default_delete. A client-supplied template argument D shall be a function object type, ...
...
template &lt;class T, class D = default_delete&lt;T&gt; &gt;
class unique_ptr
{
    #if defined(BOOST_MOVE_DOXYGEN_INVOKED)
    public:
    unique_ptr(const unique_ptr&) = delete;
    unique_ptr& operator=(const unique_ptr&) = delete;
    private:
    #else
    BOOST_MOVABLE_BUT_NOT_COPYABLE(unique_ptr)
    ...

    public: 
    ~unique_ptr()
    {  if(m_data.m_p) m_data.deleter()(m_data.m_p);   }
    ...

    void swap(unique_ptr& u) BOOST_NOEXCEPT 
    {
        ::boost::adl_move_swap(m_data.m_p, u.m_data.m_p);
        ::boost::adl_move_swap(m_data.deleter(), u.m_data.deleter());
    }
    ...
};
</span></code></pre></div>

Note: Function template
<ul>
 <li>The format for declaring function template with type parameters is: <br> template &lt;class identifier&gt; function_declaration; <br> template &lt;typename identifier&gt; function_declaration; </li>
 <li>A template parameter is a special kind of parameter that can be used to pass a type as argument.</li>
</ul>


### Simple Examples ###

<ol>
 <li>Assigning static allocation into dynamic allocation (C++11)</li>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb">void Exam::SmartPointer() { 
      constexpr int SIZE = 8; 
      static const char chars[] = { 'B', 'e', 'S', 'm', 'a', 'r', 't', '!'  };
      std::unique_ptr<char[]> arr(new char[SIZE); 
      for(int i = 0; i < SIZE; ++i) {
          arr[i] = chars[i];
          std::cout << arr[i] << "; ";
      }
      std::cout << std::endl;
}</span></code></pre></div>
 <li>Using make_unique for initializing unique_ptr (C++14)</li>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb">struct Vec3
{
    int x, y, z;
    Vec3(): x(0), y(0), z(0) {}
    Vec3(int x, int y, int z): x(x), y(y), z(z) {} 
    friend std::ostream& operator<<(std::ostream& os, Vec3& v) {   
        return os << '{' << "x:" << v.x << " y:" << v.y << " z:" << v.z  << '}';  
    }
};

void Exam::SmartPointerMakeUnique() {
    // Use the default constructor.
    std::unique_ptr&lt;Vec3&gt; v1 = std::make_unique&lt;Vec3&gt;();
    // Use the constructor that matches these arguments
    std::unique_ptr&lt;Vec3&gt; v2 = std::make_unique&lt;Vec3&gt;(0, 1, 2);
    // Create a unique_ptr to an array of 5 elements
    std::unique_ptr&lt;Vec3[]&gt; v3 = std::make_unique&lt;Vec3[]&gt;(5);

    std::cout << "V1 make_unique&lt;Vec3&gt;():      " << *v1 << '\n'
              << "V2 make_unique&lt;Vec3&gt;(0,1,2): " << *v2 << '\n'
              << "V3 make_unique&lt;Vec3[]&gt;(5):   " << '\n';
    for (int i = 0; i < 5; i++) {
        std::cout << "     " << v3[i] << '\n';
    }
}</span></code></pre></div>
</ol>

## Reference ##

[1] [cplusplus: std::unique_ptr](https://www.cplusplus.com/reference/memory/unique_ptr/?kw=unique_ptr)

[2] [Smart Pointers — unique_ptr, shared_ptr, weak_ptr](https://blog.heron.me/smart-pointers-unique-ptr-shared-ptr-weak-ptr-f1ca97cf5ba6)

[3] [C++ Primer (5th Edition)](https://www.amazon.com/Primer-5th-Stanley-B-Lippman/dp/0321714113)

[4] [cppreference: std::make_unique](https://en.cppreference.com/w/cpp/memory/unique_ptr/make_unique)

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:qazqazqaz850@gmail.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)
