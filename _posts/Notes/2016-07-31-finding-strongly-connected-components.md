---
layout: post
title:  "Finding Strongly Connected Components (Directed Graph)"
category: Notes
---

## Problem Statement

Given a **directed** graph with `V` nodes and `E` edges, find, if any, its
Strongly Connected Components (SCC)

A **SCC** is defined as a sub-graph where for every pair of vertices `u`
and `v`, there exists a path from `u` to `v` and from `v` to `u`.

**Example:** A communication network, where `x` people continiously talk
to each other on the phone, can be an application of SCC.

An illustration of a graph with multiple SCCs can be found in [this
Wikipedia article](https://en.wikipedia.org/wiki/Strongly_connected_component).

*__Note:__ If a graph's SCC are contracted (replaced by larger vertices), 
they form a DAG.*

## General Idea | $$ O(V+E) $$

This problem can be solved using [Tarjan's strongly connected components
algorithm](https://en.wikipedia.org/wiki/Tarjan%27s_strongly_connected_components_algorithm).

The crutch of this algorithm is to use `DFS` to produce a `DFS` tree/forest.
Strongly connected components of the graph will form subtrees in the generated
`DFS` Tree. We can then find the root of such subtrees and print all its nodes.

Similar to how we [find articulation points and bridges](http://shadyf.com/blog/notes/2016-07-30-finding-articulation-points-and-bridges/), 
we'll keep track of two `int` arrays, `dfs_low[]` and `dfs_num[]`. For a node `u`,
if `dfs_low[u] == dfs_num[u]`, `u` is the the root of a subtree i.e `u` and its children
form a strongly connected component.

To keep track of each node we visit in the `DFS` tree, we use a stack `result`.
`result` will be used for figuring out which nodes belong `u`'s subtree.

## Code

C++11 code below.

{% highlight c++ %}
int N, M; // Vertices and Edges
int dfs_num_counter, numSCC;
vector<bool> visited;
vector<int> dfs_num;
vector<int> dfs_low;
vector< vector<int> > adj_list;
stack<int> result;

void tarjanSCC(int u)
{
    dfs_num[u] = dfs_low[u] = dfs_num_counter++;
    result.push(u);     // push u to the result stack
    visited[u] = true;      // mark u as visited
    for (int i = 0; i < adj_list[u].size(); i++)
    {
        int v = adj_list[u][i];
        
        // Do DFS if node not previously discovered
        if (dfs_num[v] == -1)
            tarjanSCC(v);
        // Condition for update
        if(visited[v])
            dfs_low[u] = min(dfs_low[u], dfs_low[v]);
    }
    
    // After doing DFS, check if current node is the root of the SCC
    if(dfs_low[u] == dfs_num[u])
    {
        numSCC++;   // increment number of SCC
        
        // pop all the components of the SCC from the result stack
        while(true)
        {
            int v = result.top();
            printf("%d ", v);   // print the SCC
            result.pop();
            visited[v] = false;
            if(u == v) break;
        }
        printf("\n");
    }
}

// Example main usage
int main()
{
    for(int i = 0; i < N; i++)
        if(dfs_num[i] == -1)
            tarjanSCC(i);
}
{% endhighlight %}
