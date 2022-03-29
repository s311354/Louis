---
layout: post
title:   "Overview of Basic Graph Traversal Algorithm and Google testing"
date:    2022-01-14
tags:    [C_C_plus_plus, Algorithms]
---
[UPDATED: 2022/03/21]

"Graph theory is the study of graphs, which are mathematical structures used to model pairwise relations between objects. A graph in this context is made up of vertices (also called nodes or points) which are connected by edges (also called links or lines). A distinction is made between undirected graphs, where edges link two vertices symmetrically, and directed graphs, where edges link two vertices asymmertically. Graphs are one of the principal objects of study in discrete mathematics." from Wiki page.

## Brief ##
Recently, I explored the implemenation of graphs and BFS/DFS, and discovered its algorithm. It should be grateful that I have learned the fundamentals of mathematics and programming in college, so that I could learn and get a basic understanding of how the graph traversal algorithm work, and further make the computer do it. In this post, I would like to discuss the basic concepts, practice the simple implemenation of BFS and DFS with the directed graph and use googletest to do the unit test.

## Graph Search Algorithm ##
In computer science, a graph is an abstract data type that is meant to implement the undirected fraph and directed graph concept from the field of a graph theory. A graph data structure consists of a finite set of vertices, together with a set of unordered or ordered paired of these vertices. The graph search (also known as graph traversal) algorithm uses a recursion and linked list based stack to determine a route from a single point on a graph to another single point on a graph. In addition to the structure, there are two basic types of graph search: breadth-first search and depth-first search. The major difference between those two type is that the data structure requires a queue (BFS) or stack (DFS). 

Here, I recapped and practiced the [simple C++ BFS/DFS implemenation][grapg] with directed graph initially inspired by [Practice Directed Graph][directedgraph] then reworked. The basic functions for the directed graph are as follows:
<figure><center><img src="{{ site.baseurl }}/picture/graph_search.png" width="40%"> Ordered paired, Unweight and Directed Graph Database</center></figure>
- BFS (Breadth-first search): use queue and traverse all the connected nodes
- DFS (Depth-first search): use stack and traverse all the connected nodes
- Detect Cycle in a Directed Graph: check whether the graph contains a cycle or not
- Iterate in a Directed Graph:  pointing to all of vertices in the directed graph

<details markdown=block>
<summary markdown=span>*directedGraph.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">int main(int argc, char *argv[])
{    // Directed Graph
    auto graph_node = new GraphDirected(GraphRepresentation::kRepresentationTypeList);
    graph_node->AddEdge(0, 1);
    graph_node->AddEdge(0, 5);
    graph_node->AddEdge(1, 2);
    graph_node->AddEdge(2, 4);
    graph_node->AddEdge(2, 6);
    graph_node->AddEdge(3, 2);
    graph_node->AddEdge(5, 8);
    graph_node->AddEdge(6, 5);
    graph_node->AddEdge(7, 5);

    graph_node->DFS();
    graph_node->BFS();
}</span></code></pre></div></details>

### Breadth-first Search ###
Breadth First Search is traversing algorithm where you should start traversing from a selected node (source or parent node) and traverse the graph layerwise thus exploring the neighbour nodes. In other words, it starts at the tree root and explores all nodes at the present depth prior to moving on to the nodes at the next depth level. In addition to the traverse, BFS might be the easiest one to understand because the only data structure it requires is a queue. Here is the output of BFS traversal route in my implemenation:

<details markdown=block>
<summary markdown=span>*print statement*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">---------------------
BFS:
0 1 5 2 8 4 6 1 2 3 4 5 6 7 8</span></code></pre></div></details>

### Depth-first Search ###
Depth First Search is a recursive algorithm that uses the idea of backtracking. It involves searches of all the nodes by going ahead if possible, else by backtracking. In other words, it starts at the root (selectinh some arbitrary node) and explores as far as possible along each branch before backtracking. Here is the output of DFS traversal route in my implemenation:

<details markdown=block>
<summary markdown=span>*print statement*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">---------------------
DFS:
0 5 8 1 2 6 4 1 2 3 4 5 6 7 8</span></code></pre></div></details>

## Exercises ##
<h6><ol>
    <li><a href="#exercise1">Exercise 1 - Print Binary Tree by BFS</a></li>
    <li><a href="#exercise2">Exercise 2 - Shortest Distance from All Buildings by BFS</a></li>
    <li><a href="#exercise3">Exercise 3 - Critical Connections in a Network by DFS</a></li>
    <li><a href="#exercise4">Exercise 4 - Maximum Length of a Concatenated String with Unique Characters by DFS</a></li>
    <li><a href="#exercise5">Exercise 5 - Surrounded Regions by BFS</a></li>
</ol></h6>

### <a name="exercise1">Exercise 1. - Print Binary Tree by BFS  (Directed Graph)</a>  ###
The basic functions for this simple exercise is as follows:
- Pass through the binary tree's object.
- Print out the binary tree by BFS (Breadth-first search) - use queue and traverse all the nodes

<details markdown=block>
<summary markdown=span>*printBFS.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">void PrintBFS(BSTNode * node)
{
    std::queue&lt;BSTNode*&gt; node_queue;
    BSTNode* current;
    node_queue.push(node);
    while (! node_queue.empty()) {
        current = node_queue.front();
        node_queue.pop();
        if (current != nullptr) {
            std::cout &lt;&lt; current-&gt;data &lt;&lt; " ";
            if (current-&gt;left != nullptr) node_queue.push(current-&gt;left);
            if (current-&gt;right != nullptr) node_queue.push(current-&gt;right);
        }
    }
}</span></code></pre></div></details>

### <a name="exercise2">Exercise 2 - Shortest Distance from All Buildings by BFS (Directed Graph)</a> ###
You want to build a house on an empty land which reaches all buildings in the shortest amount of distance. You can only move up, down, left, and right. You are given a 2D grid of values 0, 1, or 2, where:
- Each 0 marks an empty land which you can pass by freely. 
- Each 1 markd a building which you cannot pass through. 
- Each 2 marks an obstacle which you cannot pass through. 

Example: Input: [[1,0,2,0,1],[0,0,0,0,0],[0,0,1,0,0]], Output: 7

Explanation:
In this example, there are three buildings at (0,0), (0,4), (2,2), and an obstacle at (0,2).
<figure><center><img src="{{ site.baseurl }}/picture/shortest_distance.png" width="30%"></center></figure>
The point (1,2) is an ideal empty land to build a house, as the total travel distance of 3+3+1=7 is minimal. So return 7.

#### Solution ####
<details markdown=block>
<summary markdown=span>*shortestDistance.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">int Solutions::shortestDistance( std::vector&lt; std::vector&lt;int&gt; &gt; & grid )
{
    int row = grid.size(), column = grid[0].size();
    std::vector&lt; std::vector&lt;int&gt; &gt; distance(row, std::vector&lt;int&gt;(column, 0));
    std::vector&lt; std::vector&lt;int&gt; &gt; visit(row, std::vector&lt;int&gt;(column, 0));
    int num_building = 0, ans = INT_MAX;

    // do BFS
    for (int i = 0; i &lt; row; ++i) {
        for (int j = 0; j &lt; column; ++j) {
            // parent node (building)
            if (grid[i].at(j) == 1) {
                num_building ++;
                auto tmp_grid = grid;
                bfs(i, j, tmp_grid, distance, visit);
            }
        }
    }
    // find the shortest distance
    for (int i = 0; i &lt; row; ++i) {
        for (int j = 0; j &lt; column; j++) {
            if (visit[i].at(j) == num_building)
                ans = std::min(ans, distance[i].at(j));
        }
    }
    return ans == INT_MAX ? -1: ans;
}
void Solutions::bfs(int column, int row, std::vector&lt; std::vector&lt;int&gt; &gt; &grid, 
                    std::vector&lt; std::vector&lt;int&gt; &gt; &distance, std::vector&lt; std::vector&lt;int&gt; &gt;  &visit)
{
    // assigns starting point into parent node
    std::queue&lt; std::pair&lt;int, int&gt; &gt; to_visit; // BFS
    to_visit.push( std::pair&lt;int, int&gt;(column, row));
    int step = 0;

    // traversing from source (parent node)
    while (!to_visit.empty()) {
        // exploring 2D grid
        int curDepth = to_visit.size();
        for (int i = 0; i &lt; curDepth; ++i) {
            int xx = to_visit.front().first;
            int yy = to_visit.front().second;
            to_visit.pop();

            // meet the boundary
            if (xx == grid.size() || xx &lt; 0 || yy == grid[0].size() || yy &lt;0) continue;
            // Only empty land which you can pass by freely
            if (step != 0 && grid[xx].at(yy) != 0) continue;

            // Update Status
            visit[xx].at(yy)++; //how many visitor have visited here
            distance[xx].at(yy) += step;
            grid[xx].at(yy) = -1; // visited
            to_visit.push(std::pair&lt;int, int&gt;(xx+1, yy)); // Up
            to_visit.push(std::pair&lt;int, int&gt;(xx-1, yy)); // Down
            to_visit.push(std::pair&lt;int, int&gt;(xx, yy+1)); // Right
            to_visit.push(std::pair&lt;int, int&gt;(xx, yy-1)); // Left
        }
        step ++;
    }
}</span></code></pre></div></details>

The solution was initially inspired by [Shortest Distance][shortest] and then reworked. The basic functions for caculating the shortest distanc are as follows:
- Traversing 2D grid by BFS algorithm
- Store visited count and distance between two buildings

#### Unit Test by Google Testing ####
<details markdown=block>
<summary markdown=span>*shortestDistanceTest.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">TEST_F(SolutionsTest, ShortestDistanceTest) 
{
    /* Declare the Unit Test object */
    leetcode::Solutions solutions;
    std::vector &lt; std::vector&lt;int &gt; &gt; grid = { {1, 0, 2, 0 ,1}, {0, 0, 0, 0, 0}, {0, 0, 1, 0, 0} };
    EXPECT_EQ( 7, solutions.shortestDistance(grid));
}</span></code></pre></div></details>

### <a name="exercise3">Exercise 3. - Critical Connections in a Network by DFS (Undirected Graph)</a> ###
There are n servers numbered from 0 to n-1 connected by undirected server-to-server connections forming a network where connections[i] = [a, b] represents a connection between servers a and b. Any server can reach any other server directly or indirectly through the network.

A critical connection is a connection that, if removed, will make some server unable to reach some other server.

Return all critical connections in the network in any order.

<figure><center><img src="{{ site.baseurl }}/picture/criticalNets.png" width="40%"></center></figure>
Example: Input: n = 4, connections = [[0,1],[1,2],[2,0],[1,3],[3,4]], Output: [[3,4],[1,3]]

#### Solution ####
<details markdown=block>
<summary markdown=span>*criticalConnections.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">void undirected_dfs(int curr, int parent, int visited, 
                            std::vector&lt; std::vector&lt;int&gt; &gt; & undirectedgraph, 
                            std::vector&lt;int&gt;& low, std::vector&lt; std::vector&lt;int&gt; &gt; &bridge){
    low[curr] = visited ++;
    // Exploring the neighbor node
    for (auto & nextnode : undirectedgraph[curr]) {
        if ( nextnode == parent)
            continue;
        // unvisited (Depth-first Search)
        if (low[nextnode] == 0) undirected_dfs(nextnode, curr, visited, undirectedgraph, low, bridge);
        // Assign low value to current node (circle back around to reach)
        low[curr] = std::min(low[curr], low[nextnode]);
        // Determine the bridge
        if (low[nextnode] == visited ) bridge.push_back({curr, nextnode});
    }
}
std::vector&lt; std::vector&lt;int&gt; &gt; Solutions::criticalConnections(int n, 
                            std::vector&lt; std::vector&lt;int&gt; &gt; & connections)
{
    std::vector&lt; std::vector&lt;int&gt; &gt; undirectedgraph (n);
    // constructing undirected graph
    for (auto & elem : connections) {
        undirectedgraph[elem[0]].push_back(elem[1]);
        undirectedgraph[elem[1]].push_back(elem[0]);
    }
    std::vector&lt; std::vector&lt;int&gt; &gt; bridge;
    std::vector&lt;int&gt; low(n);
    undirected_dfs(0, -1, 1, undirectedgraph, low, bridge);
    return bridge;
}</span></code></pre></div></details>

The solution was initially inspired by [Leetcode's Discuss 1: Critical Network][critical1] and [LeetCode's Discuss][critical2], and then reworked. The basic functions for determining critical connections are as follows:
- Constructing undirected graph
- Do a recursive DFS traversal, labeling node with increasing visited time and track the smallest low link value
- Determine critical connection (bridge) according to when the low link value is equal to visited time. 

Note that: 
- Low link value of a node is defined as the smallest visited time from current node when doing a DFS, including itself.
- Bridge in graphy theory is any edge in a graph whose removal increases the number of connected components.

#### Unit Test by Google Testing ####
<details markdown=block>
<summary markdown=span>*criticalConnectionsTest.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">TEST_F(SolutionsTest, criticalConnectionsTest) 
{
    /* Declare the Unit Test object */
    leetcode::Solutions solutions;
    std::vector&lt; std::vector&lt;int&gt; &gt; connection = { {0, 1}, {1, 2}, {2, 0}, {1, 3}, {3, 4} };
    int n = 5;
    std::vector&lt; std::vector&lt;int&gt; &gt; expected_value = { {3, 4} , {1, 3} };
    EXPECT_EQ(expected_value, solutions.criticalConnections(n, connection));
}</span></code></pre></div></details>

### <a name="exercise4">Exercise 4. - Maximum Length of a Concatenated String with Unique Characters by DFS (Undirected Graph)</a>  ###
You are given an array of strings arr. A string s is formed by the concatenation of a subsequence of arr that has unique characters.

Return the maximum possible length of s.

A subsequence is an array that can be derived from another array by deleting some or no elements without changing the order of the remaining elements.

<figure><center><img src="{{ site.baseurl }}/picture/maxlength.png" width="20%"></center></figure>

Example 1:

Input: arr = ["un","iq","ue"]
Output: 4
Explanation: All the valid concatenations are:
- ""
- "un"
- "iq"
- "ue"
- "uniq" ("un" + "iq")
- "ique" ("iq" + "ue")

Maximum length is 4.

#### Solution ####
<details markdown=block>
<summary markdown=span>*isUniqieString.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">int Solutions::maxLength( std::vector&lt; std::string&gt; & arr)
{
    int len = 0;
    if (arr.size() &gt; 0) return 0;
    if (arr.size() == 1) return arr[0].size();
    checkLen( arr, "", 0, len)
    return len;
}
// undirected DFS ( graph of string )
void Solutions::checkLen( std::vector&lt;std::string&gt; & arr, std::string graphstr, int index, int& count )
{
    if (isUniqieString(graphstr)) {
        count = graphstr.size() &gt; count ? graphstr.size(): count;
    }
    // recursive
    for (int i = index; i &lt; arr.size(); ++i) {
        checkLen(arr, graphstr+arr[i], i+1, count);
    }
}
bool Solutions::isUniqieString(std::string s)
{
    for (auto & elem : s) {
        if (std::count(begin(s), end(s), elem) &lt; 1) return false;
    }
    return true;
}</span></code></pre></div></details>
The basic functions for determining maximum possible length of concatenated string are as follows:
- Concatenated subsequence of string by DFS
- Check the concatenated string is unique characters
- Determine the maximum possible length of concatenated string

#### Unit Test by Google Testing ####
<details markdown=block>
<summary markdown=span>*maxLengthTest.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">TEST_F(SolutionsTest, maxLengthTest) 
{
    /* Declare the Unit Test object */
    leetcode::Solutions solutions;
    std::vector&;t; std::string&gt; arr {"un", "iq", "ue"};
    int expected_value = 4;
    EXPECT_EQ(expected_value, solutions.maxLength(arr));

    arr = {"abc", "yyy", "def", "csv"};
    expected_value = 6;
    EXPECT_EQ(expected_value,solutions.maxLength(arr));

    arr = {"eva", "jqw", "tyn", "jan"};
    expected_value = 9;
    EXPECT_EQ(expected_value,solutions.maxLength(arr));

    arr = {"photato", "kayak", "banana", "racecar"};
    expected_value = 0;
    EXPECT_EQ(expected_value,solutions.maxLength(arr));
}</span></code></pre></div></details>


### <a name="exercise5">Exercise 5. - Surrounded Regions by BFS (Undirected Graph)</a>  ###
Given an m x n matrix board containing 'X' and 'O', capture all regions that are 4-directionally surrounded by 'X'.

A region is captured by flipping all 'O's into 'X's in that surrounded region.

<figure><center><img src="{{ site.baseurl }}/picture/surrounded.png" width="60%"></center></figure>

Example: Input: board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]
Output: [["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]

#### Solution ####
<details markdown=block>
<summary markdown=span>*surroundedRegions.cc*</summary>
<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 60%">std::vector&lt; std::vector&lt;char&gt; &gt; Solutions::surroundedRegions( std::vector&lt; std::vector&lt;char&gt; &gt; & board)
{
    std::queue&lt; std::pair&lt;int,int&gt; &gt; to_visit;
    int row = board.size(), column = board[0].size();
    //Getting boundary O's
    for(int i=0; i&lt;row ; i++)
    {
        if(board[i][0]=='O') board[i][0]='U', to_visit.push( {i,0} );
        if(board[i][column-1]=='O') board[i][column-1]='U', to_visit.push( {i,column-1} );
    }
    for(int i=1; i&lt;column-1; i++)
    {
        if(board[0][i]=='O') board[0][i]='U', to_visit.push( {0,i} );
        if(board[row-1][i]=='O') board[row-1][i]='U', to_visit.push( {row-1, i} );
    }
    // 2-D Grid BFS which's parent node starts from boundary unsorrounded node
    while(!to_visit.empty())
    {
        int curDepth = to_visit.size();
        while(curDepth--)
        {
            int xx=to_visit.front().first, yy=to_visit.front().second;
            to_visit.pop();
            if(xx+1 &lt; row) if(board[xx+1][yy] == 'O') board[xx+1][yy] = 'U', to_visit.push( {xx+1, yy} );   // RIGHT
            if(xx-1 &gt;= 0) if(board[xx-1][yy] == 'O') board[xx-1][yy] = 'U', to_visit.push( {xx-1, yy} );    // LEFT
            if(yy+1 &lt; column) if(board[xx][yy+1] == 'O') board[xx][yy+1] = 'U', to_visit.push( {xx, yy+1}); // UP
            if(yy-1 &gt;= 0) if(board[xx][yy-1] == 'O') board[xx][yy-1] = 'U', to_visit.push({xx, yy-1});      // DOWN
        }
    }
    //all the unsorrounded O's are re-entered
    for(int i=0; i &lt; row; i++)
        for(int j=0; j &lt; column; j++)
            board[i][j] = board[i][j] == 'U' ? 'O' : 'X';
}</span></code></pre></div></details>
The basic functions for determining unsurrounded regions are as follows:
- Get boundary O's nodes and identify those nodes as unsurrounded node 
- Start from unsurrounded node and travers 2D grid by BFS algorithm 
- Determine all of unsurrounded nodes and set those as '0'

=========== To be continued…. ==========

## Reference ##
[1] Wiki: [Graph Theory](https://en.wikipedia.org/wiki/Graph_theory), [Breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search), [Depth-first search](https://en.wikipedia.org/wiki/Depth-first_search)

[2] Hackerearth: [Breadth-first search](https://www.hackerearth.com/practice/algorithms/graphs/breadth-first-search/tutorial/), [Depth First Search](https://www.hackerearth.com/practice/algorithms/graphs/depth-first-search/tutorial/)

[3] [Google testing](https://github.com/google/googletest)

[4] [Tarjan’s Algorithm: Strongly Connected Components](https://emre.me/algorithms/tarjans-algorithm/)

[5] [Wiki: Bridge (graph theory)](https://en.wikipedia.org/wiki/Bridge_(graph_theory))

[infotheory]:https://en.wikipedia.org/wiki/Information_theory "https://en.wikipedia.org/wiki/Information_theory"

[grapg]:https://github.com/s311354/practice_common_alogrithm/tree/Master/graphs "https://github.com/s311354/practice_common_alogrithm/tree/Master/graphs"

[directedgraph]: https://github.com/jwasham/practice-cpp/tree/master/graphs "https://github.com/jwasham/practice-cpp/tree/master/graphs"

[shortest]:https://github.com/SnoozeZ/LeetCode/blob/master/C%2B%2B/317%20Shortest%20Distance%20from%20All%20Buildings.cpp "https://github.com/SnoozeZ/LeetCode/blob/master/C%2B%2B/317%20Shortest%20Distance%20from%20All%20Buildings.cpp"

[critical1]: https://leetcode.com/problems/critical-connections-in-a-network/discuss/929563/C%2B%2B-oror-using-Tarjan's-algorithmoror-Intuition-of-Tarjan's-algo-(video-link) "https://leetcode.com/problems/critical-connections-in-a-network/discuss/929563/C%2B%2B-oror-using-Tarjan's-algorithmoror-Intuition-of-Tarjan's-algo-(video-link)"

[critical2]:https://leetcode.com/problems/critical-connections-in-a-network/discuss/1376800/C%2B%2B-oror-DFS-oror-Easy-Solution-oror-Tarjan's-Algorithm-oror-Graph-oror-85 "https://leetcode.com/problems/critical-connections-in-a-network/discuss/1376800/C%2B%2B-oror-DFS-oror-Easy-Solution-oror-Tarjan's-Algorithm-oror-Graph-oror-85"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:qazqazqaz850@gmail.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
