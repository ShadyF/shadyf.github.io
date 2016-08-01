---
layout: post
title:  "Depth First Search (DFS)"
category: Notes
---
## Problem Statement

Given a tree/graph with `V` vertices and `E` edges, search for node `X`

## General Idea | $$ O(V+E) $$

The main idea behind **Depth** First Search is to explore a node's children
first (go as **deep** as possible) before going back to the node's siblings.

For each node `u` in the graph, DFS searches through all its children/neighbours
using the `AdjList` array.

A `visited` array is also maintained to prevent cycles from occuring while searching.
*i.e* if a node has already been visited once, it will be skipped to prevent an infinite
loop from taking place.

## Code

C++ snippet below.
{% highlight C++ %}
vector<bool> visited;
vector< vector<int> > AdjList;

void dfs(int u)
{
    visited[u] = true;
    for (int j = 0; j < (int) AdjList[u].size(); j++)
    {
        //v.first -> edge connection v.second -> weight of edge
        pair<int, int> v = AdjList[u][j];
        if (visited[v] == false)
            dfs(v.first);
    }
} // for simple graph traversal, we ignore the weight stored at v.second

fill(visited.begin(), visited.end(), false);
{% endhighlight %}
