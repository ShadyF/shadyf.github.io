---
layout: post
title:  "Bipartite Graph Check"
category: Notes
---

## Problem Statement

Given a graph with `N` nodes and `E` edges, check whether the graph is
bipartite or not.

A bipartite graph is a graph where for each edge `(u, v)`, either `u` belongs
to **U** and `v` belong to **V**, or `u` belongs to **V** or `v` belongs
to **U**, where **U** and **V** are sets.

In other words, a graph is bipartite if it can be colored using only 2 colors,
where the nodes' colors are alternating.

Example illustrations can be found on [*Wikipedia*](https://en.wikipedia.org/wiki/Bipartite_graph)

*__Note:__ A graph with odd cycles is __cannot__ be bipartite.*

## General Idea | $$ O(V+E) $$

Checking whether or not a graph is bipartite can be accomplished by attempting
to color the graph using only 2 colors. If no color conflicts arise during
this process, the graph is bipartite.

This algorithm can be implemeted using either `DFS` or `BFS`.
 
 ## Code
 
 C++11 code below of a `BFS` implementation
 
{% highlight c++ %}
vector<int> color;
queue<int> q;
bool isBipartite;

void BFS(int u)
{
    q.push(u);
    color[u] = 0;   //two colors are 0 and 1
    while (!q.empty() && isBipartite)
    {
        int u = q.front();
        q.pop();
        for (int i = 0; i < adj_list[u].size(); i++)
        {
            int v = adj_list[u][i];
            
            // if v not colored yet
            if (color[v] == -1)
            {
                // set to opposite color of u
                color[v] = 1 - color[u];
                q.push(v);        //enqueue it
            }

            // if v is the same color as you, graph cannot be bipartite
            else if (color[u] == color[v])
            {
                isBipartite = false;
                return;
            }
        }
    }
}

// Example main usage
int main()
{
    isBipartite = true;
    
    // Check isBipartite on each loop iteration
    // Prevents needless loop iterations if graph
    // was found to be NOT bipartite
    for (int i = 0; i < nodes && isBipartite; i++)
        if (color[i] == -1)
            BFS(i);
}
{% endhighlight %}

## Variation

A common variation to this problem is, for example, given a city with `V` 
intersections and `E` streets, find the minimum numbers of communication towers
needed to cover the whole city, given that a communication tower can cover
the current intersection it is on and all the streets and intersections it is connected
to.

Wording plays a huge role when encountering this variation as we can, for example,
replace communication tower with **guard** and the problem would still be valid
and have the same solution.

## General Idea of Variation

The general idea is the same as the original problem. However, this time around,
we'll keep track of the counts of both colors, where the colors in this case would
represent the **towers** or **guards** of the problem.

At the end, we'll take the minimum of both colors and we'll end up with our
answer.

## Code
C++11 code below

{% highlight c++ %}
// To check min amount of beacons/guards/radar needed to cover
// intersections and roads.
// This is a popular variation of bipartite graph check problem
// Color the graph using two colors while keeping track of the count of both
// return the min of both colors

int BFS(int u)
{
    // tot keeps track of total number of nodes
    // m keeps track of a single color
    // at the end, m -> count of a single color
    // tot-m -> count of second color
    q.push(u);
    color[u] = 0;
    int tot = 0, m = 0;
    while (!q.empty())
    {
        int u = q.front();
        q.pop();
        tot++;
        m = color[u] == 0 ? m + 1 : m;
        for (int i = 0; i < adj_list[u].size(); i++)
        {
            int v = adj_list[u][i];
            if (color[v] == -1)
            {
                color[v] = 1 - color[u];
                q.push(v);
            }
            else if (color[u] == color[v])
                return -1;
        }
    }
    if (tot == 1) return 1;
    return min(m, tot - m);
}
{% endhighlight %}

