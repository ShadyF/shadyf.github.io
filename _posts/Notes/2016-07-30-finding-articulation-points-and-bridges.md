---
layout: post
title:  "Finding Articulation Points and Bridges (Undirected Graph)"
category: Notes
---

## Problem Statement

Given an **undirected** graph with `V` nodes and `E` edges, find a node `v`
(called an *`Articulation Point`* or a *`Cut Vertex`*) or an edge `e` (*`Bridge`*)
such that its removal would cause the graph to be disconnected.

**Example:** Given a computer network, find a computer (node) whose malfunction
would cause the network to be severed (diconnected).

*__Note:__ A graph without any articulation points is said be be Biconnected*

## General Idea | $$ O(V+E) $$

The general idea is to use a variation of `DFS`, allowing us to use the the
implicit *DFS Tree* formed to decide whether or not a node/edge is an
articulation point/bridge.

A node `u` is considered an articulation point in one of two cases:

- `u` **is** the root of the DFS Tree and has **more** than one child (If 
it had only one child, its removal would simply mean the graph is **not** disconnected,
it just has one less node in it.)
- `u` **is not** the root of the DFS Tree but has a child node `v` such that `v` has
a *back-edge* to one of `u`'s ancestors. (i.e there exists a path between the
two "to be disconnected" components)

To make this algorithm work, we'll maintain two `int` arrays, `dfs_num[]` and
`dfs_low[]`

- `dfs_num[u]` will store the iteration count of when `u` was first visited.
- `dfs_low[u]` will store the lowest iteration count vertex `u` can reach from
`u`'s current *DFS Tree* (i.e `u` can only reach vertices of lower iteration count
only if a *back-edge* exists in one of its children)

The node `u` with a chlid `v` is considered an articulation point iff 
`dfs_low[v] >= dfs_num[u]` which, again, would imply that that `v` has no
*back-edge* to any of `u`'s ancestors.

When it comes to edges, an edge `e`, connecting nodes `u` and `v`,
is considered a bridge iff `dfs_low[v] > dfs_num[u]`. Notice how it's the same
condition as before but we just removed the equality.

## Code
C++11 code below.

{% highlight c++ %}
vector<int> dfs_num;
vector<int> dfs_low;
vector<int> dfs_parent;
vector<bool> articulation_vertex;
int dfs_root, root_children;

void ArticulationPoint(int u)
{
    // Initially, dfs_num = dfs_low
    dfs_num[u] = dfs_low[u] = dfs_num_counter++;
    for(int i = 0; i < adj_list[u].size(); i++)
    {
        int v = adj_list[u][i];
        
        // v was not previously visited, i.e a normal tree edge
        if(dfs_num[v] == -1)
        {
            dfs_parent[v] = u;
            
            // special case if u is root
            if(u == dfs_root) root_children++;

            ArticulationPoint(v);
            
            // To detect articulation points
            if(dfs_low[v] >= dfs_num[u])
                articulation_vertex[u] = true;
            // To detect bridges
            if (dfs_low[v.first] > dfs_num[u])
                printf(" Edge (%d, %d) is a bridge\n", u, v.first);
            
            // update dfs_low[u] if dfs_low[v] is lower
            // i.e a back-edge exists in one of u's children
            dfs_low[u] = min(dfs_low[u], dfs_low[v]);
        }
        // if v was previously visited, and v is not the parent of u
        // then a back-edge certainly exists, not a direct cycle
        // update dfs_low[u] to store dfs_num of ancestor is lower than
        // current dfs_low
        else if(v != dfs_parent[u])
            dfs_low[u] = min(dfs_low[u], dfs_num[v]);
    }
}

// Example main usage
int main() 
{
    dfs_num_counter = 0;
    dfs_num.clear(); dfs_num.resize(N, -1);
    dfs_low.clear(); dfs_low.resize(N, 0);
    dfs_parent.clear(); dfs_parent.resize(N, 0);
    articulation_vertex.clear(); articulation_vertex.resize(N, false);

    for(int i = 0; i < N; i++)
        if (dfs_num[i] == -1)
        {
            dfs_root = i; root_children = 0;
            ArticulationPoint(i);
            
            // special case for root
            articulation_vertex[dfs_root] = (root_children > 1);
        }
}
{% endhighlight %}
