---
layout: post
title:  "All-Pairs Shortest Paths Problem"
category: Notes
---

## Problem Statement

The All-Pairs Shortest Paths (APSP) problem is generally defined as the
following:

> Given a graph `G`, what is the shortest path between all pairs of vertices
in `G`?

**Example:** Given a city (graph) with junctions (vertices) and roads (edges), 
what is the shortest path from every junction in the city to all the other junctions?

## General Idea | $$ O(V^3) $$

One way to solve this problem is to make `V` calls to 
a [SSSP algorithm](http://shadyf.com/blog/notes/2016-09-13-SSSP-problem/):

- V calls of $$ O((V+E)logV) $$ for Dijkstra's = $$ O(V^3 logV) $$ if E = $$ V^2 $$
- V calls of $$ O(VE) $$ for Bellman-Ford = $$ O(V^4) $$ if E = $$ V^2 $$

An alternative $$ O(V^3) $$ solution to the APSP problem is the
[Floyd–Warshall algorithm](https://en.wikipedia.org/wiki/Floyd%E2%80%93Warshall_algorithm).

The Floyd-Warshall algorithm is a Dynamic Programming algorithm that,
in essense, builds up its solution using a bottom-up approach by gradually
allowing the usage of intermediate vertices (vertices `[0..k]`) to form the shortest paths.

Initially, we'll load our graph into an Adjacency Matrix which should now represent
the direct distance between each pair of vertices, i.e there are
no intermediate vertices between any two pair of vertices (`k = -1`).

Next, we'll allow vertex `0` (`k = 0`) to be used as an intermediate vertex
in the path between any two vertices. For vertices `i` and `j` with `k = 0`,
the shortest path could be either:

1. The shortest distance of the previous iterations (In this case, when `k = -1`, i.e a direct edge between `i` and `j`)
2. The shortest distance from `i` to 0 + the shortest distance from 0 to `j` (`0` is used as an intermediate vertex)

After looping over all the possible combinations of `i` and `j`, we'll repeat the same
process but only allowing vertex `1` (`k` is `1`) to be used as an intermediate vertex.
The shortest path between `i` and `j` with `k = 1` could be either:

1. The shorest distance computed in previous iterations (Either when `k = -1` or `k = 0`)
2. the shortest distance from `i` to `1` + the shortest distance from `1` to `j`
(the shortest distance as in the shortest computed distance from previous iterations)

We'll keep on repeating this process for `k = 2`, `k = 3`...`k = V - 1` and then,
finally, our adjacency matrix should contain all the shortest distances between any two
vertices in our graph.

### A couple of things to note
- The Floyd-Warshall algorithm works well with negative edges and can even tolerate
and detect negative edge cycles.
- The main benefit of using Floyd-Warshall's algorithm is that it's only
four to six lines long. Always consider using Floyd-Warshall when the input graph
is relatively small (`V <= 400`)

## Code

C++11 code below.

{% highlight c++ %}
int AdjMat[50][50];
const inf INF = 0x3f3f3f3f;

// Example main usage
int main() {
    memset(AdjMat, INF, sizeof(AdjMat));
    
    // AdjMat[i][j] should contain the weight of edge (i, j)
    // or INF if there is no edge after the following loop
    for (int i = 0; i < E; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        AdjMat[u][v] = w;
    }
    
    // The main FW loop, O(V^3)
    for (int k = 0; k < V; k++)
        for (int i = 0; i < V; i++)
            for (int j = 0; j < V; j++)
                // What do we do if source == destination ?
                if (i == j) AdjMat[i][j] = 0;
                
                // AdjMat[i][j] will be updated with the shorter of
                // 1. shortest distance computed in previous iterations
                // 2. the shortest distance from i to k + the shortest
                // distance from k to j
                else 
                    AdjMat[i][j] = min(AdjMat[i][j], AdjMat[i][k] + AdjMat[k][j]);
}
{% endhighlight %}

## Variations

### SSSP on Small Weighted Graphs

Instead of using Dijskta's/Bellman-Ford's algorithms to solve the SSSP problem
on a relatively small graph where `V <= 400`, we can use the much shorter
(coding wise) Floyd-Warshall algorithm.

### Printing the Shortest Paths

Unlike in BFS/Dijkstra's/Bellman-Ford's algorithms, we cannot simply
use a 1D `vector<int> p` to store the parent information of each vertex.
Instead, we need to use a 2D parent matrix to be able to properly reconstruct
the paths.

{% highlight c++ %}
void printPath(int i, int j) {
    if (i != j) printPath(i, p[i][j]);
    printf(" %d", j);
}

int main() {
    // The following assumes the proper weights have already been
    // inserts into AdjMat
    for(int i = 0; i < V; i++)
        for(int j = 0; j < V; j++)
            p[i][j] = i;
            
    // FW loop
    for (int k = 0; k < V; k++)
        for (int i = 0; i < V; i++)
            for (int j = 0; j < V; j++)
                if (i == j) AdjMat[i][j] = 0;
                else if (AdjMat[i][k] + AdjMat[k][j] < AdjMat[i][j]) {
                    AdjMat[i][j] = AdjMat[i][k] + AdjMat[k][j];
                    p[i][j] = p[k][j];      // update parent matrix
                }
                
    // print path from source to destination
    // NOTE: This will not work is the source is the same as the
    // destination (source == destination)
    printPath(s, d);
}
{% endhighlight %}

### Transitive Closure (Warshall's Algorithm)

The Transitive Closure problem is defined as the following:

> Given a graph, determine if vertex `i` is connected to vertex `j`, either
directly or indirectly.

We can solve this problem using a modified FW that utilizes bitwise operations
that come with a (significant) speedboost as well. Initially, `AdjMat[i][j]` will
contain `1` if vertex `i` is directly connected to `j`. 

After running the algorithm, we can then check if `i` is connected to `j`
either directly or indirectly by checking `AdjMat[i][j]`'s value
(`1` if connected, `0` if not connected). 

{% highlight c++ %}
// AdjMat[i][j] should be set to 1 if direct edge from i to j and
// set to 0 otherwise
for (int k = 0; k < V; k++)
    for (int i = 0; i < V; i++)
        for (int j = 0; j < V; j++)
            AdjMat[i][j] |= (AdjMat[i][k] & AdjMat[k][j]);
{% endhighlight %}

### Minimax and Maximin Problem

An excellent explanation of the maximin and minimax problem can be
found in this [Stack Overflow answer](http://stackoverflow.com/a/9023192).

In short, the maximin path (bottleneck path) revolves around find a path
from the source to the destination whose minimum-weight edge is maximized
(I visualize it as trying to clamp the lower-limit upwards as much as possible).

The minimax path represents the opposite idea - the path from source to destination
that minimizes the maximum edge weight (trying to clamp the upper-limit downwards as
much as possible).

This problem can be solved using a slight modification of FW's algorithm.
Instead of trying to figure out the length of the shortest path, to find the
minimax path we'll now have to find the minimum of the max weight of all the edges
in the path from `i` to `j` 

{% highlight c++ %}
// MINIMAX PATH

// Pair of vertices with no connection between them 
// should be initialized with INF
// i.e AdjMat[i][j] = INF if no direct edge between them
for(int k = 0; k < V; k++)
  for(int i = 0; i < V; i++)
    for(int j = 0; j < V; j++)
      AdjMat[i][j] = min(AdjMat[i][j], max(AdjMat[i][k], AdjMat[k][j]));
{% endhighlight %}

For the maximin path, we need to find the maximum of the minimum weight of
all the edges in the path from `i` to `j`.

{% highlight c++ %}
// MAXIMIN PATH

// Pair of vertices with no connection between them 
// should be initialized with -INF
// i.e AdjMat[i][j] = -INF if no direct edge between them
for(int k = 0; k < V; k++)
  for(int i = 0; i < V; i++)
    for(int j = 0; j < V; j++)
      AdjMat[i][j] = max(AdjMat[i][j], min(AdjMat[i][k], AdjMat[k][j]));
{% endhighlight %}

### Finding the Cheapest/Negative Cycle

The Floyd-Warshall algorithm can be used to detect whether a graph has a cycle,
a negative cycle and even the cheapest/smallest non-negative cycle among all
possible cycles.

To do this, we simply run the stardard FW algorithm without checking 
if `i == j` in our loop and explicitly updating `AdjMat` accordingly. We should simply 
leave the case where `i == j` to modified on its own in the main loop.

After the algorithm's execution, we will find a case where `AdjMat[i][i] != INF` if a cycle exists.

If a negative cycle exists, we will find a case where `AdjMat[i][i] < 0`

To find the cheapest non-negative cycle, we simply loop every possible
vertex `i` and find the smallest `AdjMat[i][i]`

### Finding the Diameter of a Graph

The diameter of the graph is defined as the maximum shortest path distance
between any two vertices in a graph.

To find the diameter, we simply loop over `AdjMat[i][j]` in $$ O(V^2) $$
after FW has been executed and find the maximum value.

### Finding the SCCs of a Directed Graph

Usually, to find the SCCs of a directed graph we'd use Tarjan's $$ O(V + E) $$
algorithm. However, if input graph is relatively small, we can use the much 
shorter FW algorithm to solve the same problem.

We'll first run Warhshall's algorithm (described above) in $$ O(V^3) $$ and then
use the following check: To find all the members of a SSC that contain vertex `i`,
we'll check for all other vertices `j` If `AdjMat[i][j] == 1` and `AdjMat[j][i] == 1`
which would indicate that both`i` and `j` belong to the same SCC.
