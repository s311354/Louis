---
layout: post
title: "Introduction to Dynamic_Programming"
date: 2022-04-26
tags: [Programming, C_C_plus_plus]
---

"Dynamic Programming is both a mathematical optimization method and a computer programming method. The method was developed by Richard Bellman in the 1950s and has found applicationns in numerous fields, from aerospace engineering to economics." ... from Wiki.

## Brief ##
The dynamic programming (DP) refers to simplifying a complicated problem by breaking it down into simpler sub-problems in a recursive manner and is just an optimization technique. While some decision problems cannot be taken apart this way, decisions that span several points in time do often break apart recursively. Likewise, in computer science, if a problem can be solved optimally by breaking it into sub-problems and then recursively finding the optimal solutions to the sub-problems, then it is said to have optimal substructure.  In this post, I would like to briefly discuss about the properties of dynamic programming and practice several exercises, such as schedule parallel courses, choose the best time to buy and sell stock, and choose the cheapest flights within K stops. 

### Properties ###
There are two key properties that a problem must have in order of dynamic programming to be applicable:
+ Optimal substructure:  the solution to given optimizationn problem can be obtained by the combination of optimal solutions to its sub-problems. Sub optimal substructures are usually described by means of recursion.
+ Overlapping sub-problem: the space of sub-problems must be small, that is, any recursive algorithm solving the problem should solve the same sub-problems over and over, rather than generating new sub-problems.

## Exercises ##
<h6><ol>
    <li><a href="#exercise1">Exercise 1 - Best Time to Buy and Sell Stock</a></li>
    <li><a href="#exercise2">Exercise 2 - Parallel Courses</a></li>
</ol></h6>

### <a name="exercise1">Exercise 1 - Best Time to Buy and Sell Stock</a> ###
You are given an integer array prices where prices[i] is the price of a given stock on the ith day.

On each day, you may decide to buy and/or sell the stock. You can only hold at most one share of the stock at any time. However, you can buy it then immediately sell it on the same day.

Find and return the maximum profit you can achieve.

Example: Input: prices = [7,1,5,3,6,4], Output: 7

#### Solution ####

<details markdown=block>
<summary markdown=span>*maxProfit.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">int Solutions::maxProfit( std::vector&lt;int&gt; & prices) {
    int curHold = INT_MIN, curProfit = 0;

    for (const int stockprice: prices) {
        int prevHold = curHold, preProfit = curProfit;

        // Buy the in stock market today
        curHold = std::max(prevHold, preProfit - stockprice);

        // Sell in stock market today
        curProfit = std::max(preProfit, prevHold + stockprice);
    }

    // Max profit must come from notHold
    return curProfit;
}</span></code></pre></div></details>

The solution was inspired by [LeetCode Discuss][discuss1]. The basic functions for caculating the minimal number of semesters are the same.

### <a name="exercise1">Exercise 2 - Parallel Courses II</a> ###
You are given an integer n, which indicates that there are n courses labeled from 1 to n. You are also given an array relations where relations[i] = [prevCoursei, nextCoursei], representing a prerequisite relationship between course prevCoursei and course nextCoursei: course prevCoursei has to be taken before course nextCoursei. Also, you are given the integer k.

In one semester, you can take at most k courses as long as you have taken all the prerequisites in the previous semester for the courses you are taking.

Return the minimum number of semesters needed to take all courses. The testcases will be generated such that it is possible to take every course.

Example: Input: n = 4, dependencies = [[2,1],[3,1],[1,4]], k = 2, Output: 3

#### Solution ####

<details markdown=block>
<summary markdown=span>*minNumberOfSemesters.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">int find_min_semester( std::vector&lt; std::vector&lt;int&gt; &gt; relations, std::vector&lt;int&gt; & bitmask, int mask, int k, int n) {
    // finish when all courses are taken
    if (mask == ((1 &lt;&lt; n) - 1)) return 0;
    // Memorization of taken courses
    if (bitmask[mask] != -1) return  bitmask[mask];
    // Initialize all vertices with indegree 0
    std::vector&lt;int&gt; indegree(n, 0);
    // Update the indegree
    for (int i = 0; i &lt; n; ++i) {
        // skip the courses have been taken
        if (mask & (1 &lt;&lt; i)) continue;
        for (auto &elem : relations[i]) indegree[elem] ++;
    }
    // For a mask of all courses with 0-indegree
    int temp = 0;
    for (int i = 0; i &lt; n; ++i) {
        // iterate through all the courses with zero indegree and have't been taken
        if (indegree[i] == 0 && !(mask & (1&lt;&lt;i))) temp = temp | (1&lt;&lt;i);
    }
    int courses = temp;
    // count of courses with 0-indegree
    int count = __builtin_popcount(courses);
    int semester = INT_MAX;
    if (count &gt; k) {
        // (Overlapping sub-problem) iterate through all submasks (sub-problem) of temp
        while (courses) {
            courses = (courses - 1) & temp;
            count = __builtin_popcount(courses);
            if (count != k) continue;
            semester = std::min(semester, 1 + find_min_semester(relations, bitmask, mask|courses, k, n));
        }
    } else {
        semester = std::min(semester, 1 + find_min_semester(relations, bitmask, mask|courses, k, n));
    }
    return bitmask[mask] = semester;
}
int Solutions::minNumberOfSemesters(int n, std::vector&lt; std::vector&lt;int&gt; &gt; & relations, int k) {
    // No dependencies
    if (relations.size() == 0) return n % k == 0 ? n / k: n/ k + 1;
    std::vector&lt; std::vector&lt;int&gt; &gt; adjacent(n);
    std::vector&lt;int&gt; bitmask;
    for (auto &course : relations) {
        adjacent[course[0] - 1].push_back(course[1] - 1); // graph
    }
    // initial bitmask that all of courses haven't been taken
    bitmask.assign(1&lt;&lt;n, -1);
    return find_min_semester(adjacent, bitmask, 0, k, n);
}</span></code></pre></div></details>
The solution was inspired by [LeetCode Discuss][discuss2]. The basic functions for caculating the minimal number of semesters are as follows:

+ Initialize all cities without visiting
+ DFS-based linear-time traverse neighbor city
+ Count how many set is considered a connected city

=========== To be continued…. ==========

## Reference ##
+ [Wiki: Dynamic Programming](https://en.wikipedia.org/wiki/Dynamic_programming)

[discuss1]:https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/discuss/803206/PythonJSGoC%2B%2B-O(n)-by-DP-w-Visualization "https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/discuss/803206/PythonJSGoC%2B%2B-O(n)-by-DP-w-Visualization"

[discuss2]:https://leetcode.com/problems/parallel-courses-ii/discuss/719159/DP-solution-with-memoization-and-bitmasks-With-C%2B%2B-code-20-ms-runtime "https://leetcode.com/problems/parallel-courses-ii/discuss/719159/DP-solution-with-memoization-and-bitmasks-With-C%2B%2B-code-20-ms-runtime"
