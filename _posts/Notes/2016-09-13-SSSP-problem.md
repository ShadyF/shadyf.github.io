---
layout: post
title:  "Single-Source Shortest Paths Problem"
category: Notes
---

## Problem Statement

The Single-Source Shortest Paths (SSSP) problem is generally defined as the
following:

> Given a graph `G` and a source vertex `s`, what are the shortest paths
from `s` to every other vertex in `G`?

**Example:** Given a city (graph) with junctions (vertices) and roads (edges), 
what is the shortest path from our current location at junction `s` to every
other junction in the city?

The solution to the SSSP problem varies wildly depending on the state
of the *edges* in graph. Are the edges weighted? unweighted?
Are there any negative weights? Are there any cycles?

Because of this, we'll go over a couple of different algorithms that solve
the SSSP problem.

## SSSP on Unweighted Graphs

### General Idea | $$ O(V + E) $$
If our graph's edges are unweighted (or have a constant weighted), the solution
of the SSSP problem will revolve around using the well known
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

### Code

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

## SSSP on Weighted (Positive) Graphs

### General Idea | $$ O((V+E)LogV) $$

Since the edges are weighted, we cannot use BFS since it mainly relies on
counting *direct* edges, not taking into account shorter but undirect paths.

The proper algorithm to solve this problem is [*Dijkstra's Shortest Path Algorithm*](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm).

Dijkstra's algorithm can be implemented using many different data structures (sets,
priority queues, heaps) and has multiple variations and uses ([A* algorithm](https://en.wikipedia.org/wiki/A*_search_algorithm),
[Johnson's algorithm](https://en.wikipedia.org/wiki/Johnson%27s_algorithm)).

For our purposes, we'll focus on the priority queue implementation, mainly 
due to its brevity and because priority queues are built-into C++.

To start things off, we'll use a **min** priority queue. Meaning lower (shorter) values
are at the top of the priority queue. We do this to be able to greedly pick
the shortest path. If we were to find the *longest* path, we'd use a **max**
priority queue instead.

We'll first create a priority queue of pairs, `priority_queue< pair<int, int> > pq`. 
Each pair will hold our destination vertex `u` and the distance from our source vertex `s`
to `u` (distance from `s` to `u`, `u`).

We'll also have a `vector<int> dist` to hold the minimum distance from `s` to `u`. This
is where our solution will be after we run the algorithm.

Initially, `pq` will contain our base case: (0, `s`), which is true for the source vertex.
If we had multiple source vertices (we could start from any of them), we'd add them all `pq`
before starting our main loop.

Our main loop will consist of the following:

- Greedly pop off the `front` of `pq`. For each **unvisited** `u` that appears in `front`,
it is guaranteed that `front` will also hold the shortest distance from `s` to `u`. So, if only wanted
the shortest distance from `s` to vertex 8, we'd break our loop once `front` is 8.
- Attempt to *relax* all of `u`'s neighbours. Assuming one of `u`'s neighbours is `v`, 
The *relax* operation revolves around updating `dist` by checking
if the distance from `s` to `v` via `u` is shorter than the currently known distance
from `s` to `v`.
- If we manage to relax one of `u` neighbours, we'll add the newer and shorter (distance, vertex) pair
to `pq`

A couple of things to note with this algorithm:

- Shorter distances will always appear at the top, thus is `u` has not been visited
before, the extracted distance from `pq`'s `front` is the minumum.
- This algorithm does **NOT** work in the presence of *negative* edge cycles, since
the total distance becomes lower each time the cycle is traversed, leading the algorithm to be
stuck in an infite loop. In short, do **not** use this algorithm with negative-weight edges.

### Code

C++11 code below.

{% highlight c++ %}
int V;      // number of vertices
const int INF = 0x3f3f3f3f;

vector<int> dist;
vector<vector<pair<int, int>>> AdjList;

// Min priority queue (shorter distances at top)
priority_queue<pair<int, int>, vector<pair<int, int>>,
               greater<pair<int, int>>> pq;

// Example main usage
int main()
{
    dist.clear();
    dist.resize(V, INF);
    
    // Setting the base case in both dist and pq
    dist[s] = 0;
    pq.push({0, s});
    
    while (!pq.empty()) {
        // get current shortest distance
        auto front = pq.top(); pq.pop();
        int d = front.first, u = front.second;
        
        // We can stop the algorithm if u == target
        if(u == target)
            break;
        
        // If we previously recorded a shorter distance than the curret one
        // then we skip this iteration
        // This is needed because more than one copy of the same vertex
        // can exist in pq, each with different distances
        if (d > dist[u]) continue;
        
        for(int j = 0; j < AdjList[u].size(); j++)
        {
            auto v = AdjList[u][j];
            
            // if distance from s to v via u is smaller
            // than the current distance to v
            if (dist[u] + v.first < dist[v.second])
            {
                // relax operation, we update dist
                dist[v.second] = dist[u] + v.first;
                // we push the new dist to our pq
                pq.push({dist[v.second], v.second});
            }
        }
    }
}
{% endhighlight %}

## SSSP on Weighted (Positive or Negative) Graphs

If our graph contains or even *might* contain negative-weight edges, it's better
to use the [Bellman-Ford Algorithm](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm) because,
again, using Dijkstra's algorithm with negative weights might lead to a infinite
loop due to the presence of negative cycles.

Bellman-Ford's algorithm can even detect negative cycles and report
their existence should it be necessary.

### General Idea | $$ O(VE) $$

The Bellman-Ford algorithm, like Dijkstra's, revolves around the concept of
*relaxation*. However, Dijkstra's algorithm uses a priority queue to greedily
select the closest (shortest in distance) vertex that has not been processed,
and performs this relaxation process on all of its outgoing edges.

By contrast, the Bellman-Ford algorithm simply relaxes **all** the edges,
and it does this `V - 1` times, where `V` is the number of vertices.

The reason/proof of why this works can be found in
[this Wikipedia article](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm#Proof_of_correctness).

The Bellman-Ford algorithm can also be used to detect the presence of cycles by simply
running one more iteration after the main `V - 1` times. If any changes are
made during this extra iteration then a cycle exists.

It is also worth noting that while in the worst case this algorithm runs in
$$ O(VE) $$, we can usually terminate it much earlier if an iteration of
the main loop (where we loop `V - 1` times) ends without making any changes as
subsequent iterations will not have any further effect.

### Code

C++11 code below.

{% highlight c++ %}
bool modified;
cont int INF = 0x3f3f3f3f;

// Example main usage
int main(){
    memset(dist, INF, sizeof(dist));
    dist[0] = 0;        // distance from source to souce is 0
    modified = true;    // used to check for any changes in main loop
    
    for (int i = 0; i < n - 1 && modified; i++)
    {
        modified = false;
        for (int u = 0; u < n; u++)
            for (int j = 0; j < AdjList[u].size(); j++)
            {
                auto v = AdjList[u][j];
                // Try to relax
                if (dist[u] + v.second < dist[v.first])
                {
                    dist[v.first] = dist[u] + v.second;
                    modified = true;        // changes have been made
                }
            }
    }
    
    // after running the O(VE) Bellman Ford’s algorithm shown above
    // one more pass to check if we can still relax an edge
    bool hasNegativeCycle = false;
    
    for (int u = 0; u < n; u++)
        for (int j = 0; j < (int)AdjList[u].size(); j++) {
            ii v = AdjList[u][j];
            if (dist[u] + v.second < dist[v.first])
                hasNegativeCycle = true;    // negative cycle exists!
    }
}
{% endhighlight %}