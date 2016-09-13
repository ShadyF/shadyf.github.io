---
layout: post
title:  "SSSP On Unweighted Graphs"
category: Notes
---

## Problem Statement

Given an **unweighted** graph `G`, what are the shortest paths from a
source vertex `s` to every other vertex `d` in our graph?

**Example:** Given a city (graph) with junctions (vertices) and roads (edges), 
what is the shortest path from our current location at junction `s` to every
other junction in the city, given that the time to travel between any two junctions
is the same(i.e the weight of all the roads are the same)? 

_**Note 1:** If the weights of all the edges of our graph are the same (i.e
a constant `C`), the algorithm described below will still work, we'll just have to
multiply our result with our constant `C`._

_**Note 2:** SSSP = Single-Source Shortest Path_

## General Idea | $$ O(V + E) $$

The solution of this problem will revolve around using the well known
[Breath-First Search](https://en.wikipedia.org/wiki/Breadth-first_search)
algorithm, also known as `BFS`.

A couple of things to note:

1. `BFS`, by nature, visits every vertex of a graph from a source `s` on a
*layer by layer* basis.
2. In an unweighted graph, the distance between any two neighbouring vertices connected with
an edge is 1 unit.

With these two facts in mind, we can safely say that the layer count of any vertex in our graph
is the *length* of the shortest path from our source `s` to that vertex.

Should our edges have a constant weight `C`, we simple multiply `C`
with our layer count to get the correct lenght.

To actually get the path itself, we'll have to store and reconstruct the `BFS` spanning tree.
This can be done by using a `vector<int>`. this `vector` will be used to store
the parent of each vertex `v`. Then, with a simple recursive function, we can
recursively reconstruct the shortest path using this `vector`.

_**Note:** This algorithm is suitable for graphs with V, E <= 10M to be
able to pass the TL (Time Limit)_

## Code

C++11 code below.

{% highlight c++ %}
// stores distance from s to our destination 'd'
// index represents our 'd'
vector<int> dist;

queue<int> queue;   // used by BFS
vector<int> p;      // 'parent' vector, used to store/reconstruct path

const int INF = 0x3f3f3f3f;

void printPath(int u)
{
    // NOTE: while this function directly prints the path,
    // some problems will require that we store the path first the print it
    // In these cases, we can simply replace the printf statements with
    // an insertion into a stack, which is maybe part of a vector of stacks?
    
    // base case, current node is the source
    if (u == s) { printf("%d", s); return; }
    
    // recursive: to make the output format: s -> ... -> d
    printPath(p[u]);

    printf(" %d", u);
}

//Example main usage
int main()
{
    dist.clear(); dist.resize(V, INF);
    
    dist[s] = 0;        // distance from s to s is 0 obviously
    q.push(s);          // push our source vertex to the queue
    
    // main BFS loop
    while(!q.empty())
    {
        int u = q.front(); q.pop();
        
        for(int i = 0; i < AdjList[u].size(); i++)
        {
            int v = AdjList[u][i];
            if(dist[v] == INF)
            {
                dist[v] = dist[u] + 1;
                q.push(v);
                
                // The following is needed if we intend on printing the path
                // store the parent of vertex 'v' into our 'parent' vector
                p[v] = u;
            }
        }
    }
    
    // print path from source 's' to 'd'
    printPath(d), printf("\n");
}
{% endhighlight %}


