---
layout: post
title:  "Finding connected components in a graph"
category: Notes
---

## Problem Statement

Given a graph with `V` vertices and `E` edges, find the number of [connected
components](https://en.wikipedia.org/wiki/Connected_component_(graph_theory)).

**Note:** _This is not the same as finding the number of **strongly** connected
components_

## General Idea

The general idea is to simply start traversing from any vertex (for simplicity,
we start from vertex `0`). `DFS` or `BFS` should both work fine here. A search from any
vertex `v` will find the entire connected component containing `v` before returning.

To find all the connected components of a graph, we loop through all its vertices,
starting a new `DFS` or `BFS` search whenever the loop reaches a vertex that has not
been already included in a previously found connected component.

[*Wikipedia*](https://en.wikipedia.org/wiki/Connected_component_(graph_theory)#Algorithms)

## Code

C++ code below, using `DFS`

{% highlight c++ %}

vector<bool> visited;
int number_of_connected_components = 0;

int main()
{
    //V -> each vertex from [0..V-1]
    for(int i = 0; i < V; i++)
        if(!visited[i])
        {
            number_of_connected_components++;
            dfs(i);
        }
}
{% endhighlight %}