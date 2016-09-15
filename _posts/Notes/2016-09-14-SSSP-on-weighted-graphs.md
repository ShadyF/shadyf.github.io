---
layout: post
title:  "SSSP On Weighted Graphs"
category: Notes
---

## Problem Statement

Given a **weighted** graph `G`, what are the shortest paths from a
source vertex `s` to every other vertex `d` in our graph?

**Example:** Given a city (graph) with junctions (vertices) and roads (edges), 
what is the shortest path from our current location at junction `s` to every
other junction in the city, given that the time to travel between any two junctions
differs depending on the road? (i.e the weight of all the roads are *not* the same)

## General Idea | $$ O((V+E)LogV) $$

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

## Code

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