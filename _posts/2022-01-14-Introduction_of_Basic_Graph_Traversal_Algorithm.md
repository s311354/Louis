---
layout: post
title:   "Overview of Basic Graph Traversal Algorithm"
date:    2022-01-14
tags:    [C_C_plus_plus, Algorithms]
---

"Graph theory is the study of graphs, which are mathematical structures used to model pairwise relations between objects. A graph in this context is made up of vertices (also called nodes or points) which are connected by edges (also called links or lines). A distinction is made between undirected graphs, where edges link two vertices symmetrically, and directed graphs, where edges link two vertices asymmertically. Graphs are one of the principal objects of study in discrete mathematics." from Wiki page.

## Brief ##
Recently, I explored the implemenation of graphs and BFS/DFS, and discovered its algorithm. It should be grateful that I have learned the fundamentals of mathematics and programming in college, so that I could learn and get a basic understanding of how the graph traversal algorithm work, and further make the computer do it. In this post, I would like to discuss the basic concepts and practice the simple implemenation of BFS and DFS with the directed graph.

## Graph Search Algorithm ##
In computer science, a graph is an abstract data type that is meant to implement the undirected fraph and directed graph concept from the field of a graph theory. A graph data structure consists of a finite set of vertices, together with a set of unordered or ordered paired of these vertices. The graph search (also known as graph traversal) algorithm uses a recursion and linked list based stack to determine a route from a single point on a graph to another single point on a graph. In addition to the structure, there are two basic types of graph search: breadth-first search and depth-first search. The major difference between those two type is that the data structure requires a queue (BFS) or stack (DFS). 

Here, I recapped and practiced the [simple C++ BFS/DFS implemenation][grapg] with directed graph initially extracted from [Practice Directed Graph][directedgraph] then reworked. The basic functions for the directed graph are a follows:
<figure><center><img src="{{ site.baseurl }}/picture/graph_search.png" width="40%">Unweight and Directed Graph Database</center></figure>
- BFS (Breadth-first search): use queue and traverse all the connected nodes
- DFS (Depth-first search): use stack and traverse all the connected nodes
- Detect Cycle in a Directed Graph: check whether the graph contains a cycle or not
- Iterate in a Directed Graph:  pointing to all of vertices in the directed graph

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">int main(int argc, char *argv[])
{    // Girected Graph
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
}</span></code></pre></div>

### Breadth-first search ###
Breadth First Search is traversing algorithm where you should start traversing from a selected node (source or parent node) and traverse the graph layerwise thus exploring the neighbour nodes. In other words, it starts at the tree root and explores all nodes at the present depth prior to moving on to the nodes at the next depth level. In addition to the traverse, BFS might be the easiest one to understand because the only data structure it requires is a queue. Here is the output of BFS traversal route in my implemenation:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">---------------------
BFS:
0 1 5 2 8 4 6 1 2 3 4 5 6 7 8</span></code></pre></div>

### Depth-first Search ###
Depth First Search is a recursive algorithm that uses the idea of backtracking. It involves searches of all the nodes by going ahead if possible, else by backtracking. In other words, it starts at the root (selectinh some arbitrary node) and explores as far as possible along each branch before backtracking. Here is the output of DFS traversal route in my implemenation:

<div class="language-shell highlighter-rouge"><pre class="highlight"><code class="hljs ruby"><span class="nb" style="font-size: 80%">---------------------
DFS:
0 5 8 1 2 6 4 1 2 3 4 5 6 7 8</span></code></pre></div>


## Reference ##
[1] Wiki: [Graph Theory](https://en.wikipedia.org/wiki/Graph_theory), [Breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search), [Depth-first search](https://en.wikipedia.org/wiki/Depth-first_search)

[2] Hackerearth: [Breadth-first search](https://www.hackerearth.com/practice/algorithms/graphs/breadth-first-search/tutorial/), [Depth First Search](https://www.hackerearth.com/practice/algorithms/graphs/depth-first-search/tutorial/)

[infotheory]:https://en.wikipedia.org/wiki/Information_theory "https://en.wikipedia.org/wiki/Information_theory"

[grapg]:https://github.com/s311354/practice_common_alogrithm/tree/Master/graphs "https://github.com/s311354/practice_common_alogrithm/tree/Master/graphs"

<p>**Thanks for reading! Feel free to leave the comments below or <a href="mailto:qazqazqaz850@gmail.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)**</p>
