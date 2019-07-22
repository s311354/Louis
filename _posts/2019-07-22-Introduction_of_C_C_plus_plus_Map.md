---
layout: post
title:  Introduction of Container Map in C++ Standard Template Library (STL)
tags: [C_C_plus_plus] 
---

## Brief

  Maps are associative containters that store elements formed by a combination of a key value and a mapped value, following a specific order.

  In a map, the key values are generally used to sort and uniquely identify the elements, while the mapped values store the content associated to this key, and typically implemented as binary search trees.

  The type of key and mapped value map may differ, and are grouped together in member type value_type, which is a pair type combining both:

  typedef pair< const Key, T > value_type;

## Member functions (C++ 11)

<h4><ul style="list-style-type:disc">
    <li>begin</li>
    - Return iterator to beginning
    <li>end</li>
    - Return iterator to end
    <li>size</li>
    - Return container size
    <li>insert</li>
    - Insert elements
    <li>find</li>
    - Get iterator to element
    <li>erase</li>
    - Erase elements
    <li>clear</li>
    - Clear content
</ul></h4>

## Exercise: (Leetcode) 1128. Number of Equivalent dominoes Pairs

Description:
Given a list of dominoes, dominoes[i] = [a, b] is equivalent to dominoes[j] = [c, d] if and only if either (a==c and b==d), or (a==b and b==c) - that is, one domino can be rotated to be equal to another domino.

Return the number of pair (i, j) for which 0 <= i < j < dominoes.length, and dominoes[i] is equivalent to dominoes[j].

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb">typedef pair< int, int > two_int;
int Dynamic_Array_Exercise::numEquivDominoPairs_opt(vector< vector< int > >& dominoes) {
	int result = 0;
	map< pair< int, int >, int > dict;

	for(int i=0; i<dominoes.size(); i++){
		int a = dominoes[i][0], b = dominoes[i][1];
		// Sorting
		two_int key = {min(a, b), max(a, b)};
		// Count the same pair
		dict[key] += 1;
	}

	// Calculate the arithmetic series
	for(auto it = dict.begin(); it != dict.end(); it++){
		int value = it->second;
		result += value * (value-1) / 2;
	}
	return result;
}</span></code></pre></div>


## Reference

[1] [C/C++ - Map (STL) 用法與心得完全攻略](https://mropengate.blogspot.com/2015/12/cc-map-stl.html) 

[2] [Containers: Map](http://www.cplusplus.com/reference/map/map/)
## Note
<p>If you have any constructive criticism or advises, leave the comments below or feel free to email me @qazqazqaz850@gmail.com.
Hope this post will help! :)
</p>


