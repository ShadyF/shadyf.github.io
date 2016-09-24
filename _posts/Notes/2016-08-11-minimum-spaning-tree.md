---
layout: post
title:  "Minimum Spanning Tree"
category: Notes
---

## Problem Statement

Given a connected, undirected and weighted graph `G`, find a subset of the
edges `E` such that `G` is still connected and the weight of the selected subset
is *minimal*

**Example:** Building a road network in a village such that each hut
 (vertex) is connected and the cost of connecting roads (edges) is minimal
 
## General Idea | $$ O(ElogV) $$
 
The first algorithm that solves this problem is [Kruskal's algorithm](https://en.wikipedia.org/wiki/Kruskal%27s_algorithm).
 
Kruskal's algorithm revolves around sorting the graph's edges (which should
be in an `Edge List`) based on their weights. After that, we greedily pick the smallest
edge to add spanning tree. Should this newly added edge form a cycle in our spanning tree,
we discard it.

We keep repeating this process until we've processed each edge `E` in the `Edge List`.
We should then be left with `V-1` edges representing the minimum spanning tree.

**Note:** To detect whether or not the addition of an edge to our minimum spanning
tree will cause a cycle, we can use a Union-Find Disjoint Sets data structure
as demonstrated in the code below.

## Code

C++11 code below.

{% highlight c++ %}
int n, m, mst_cost;
vector<pair<int, pair<int, int>>> EdgeList;     // (weight, (u, v))

class UnionFind
{
public:

    vector<int> p, rank;
    int numSets;
    int num;
    explicit UnionFind(int N)
    {
        num = N;
        rank.assign(N, 0);
        p.assign(N, 0);
        numSets = N;
        for (int i = 0; i < N; i++) p[i] = i;
    }

    void reset()
    {
        rank.assign(num, 0);
        p.assign(num, 0);
        numSets = num;
        for (int i = 0; i < num; i++) p[i] = i;
    }

    int findSet(int i)
    { return ( p[i] == i ) ? i : ( p[i] = findSet(p[i]) ); }

    bool isSameSet(int i, int j)
    { return findSet(i) == findSet(j); }

    int numDisjoinSets()
    {
        return numSets;
    }

    void unionSet(int i, int j)
    {
        if (!isSameSet(i, j))
        {
            int x = findSet(i), y = findSet(j);
            numSets--;
            if (rank[x] > rank[y]) p[y] = x;
            else
            {
                p[x] = y;
                if (rank[x] == rank[y]) rank[y]++;
            }
        }
    }
};

int main()
{
    EdgeList.clear();
    for (int i = 0; i < m; i++)
    {
        int u, v, w;
        cin >> u >> v >> w;
        EdgeList.push_back(make_pair(w, make_pair(u, v)));
    }
    
    // We sort the EdgeList to be able to greedly pick
    // the smallest edges in terms of weight
    sort(EdgeList.begin(), EdgeList.end());
    
    //Initializing the UnionFind Disjoint Set class
    UnionFind UF(n);
    mst_cost = 0;
    for (int i = 0; i < m; i++)
    {
        auto front = EdgeList[i];       // pair<int, ii>
        
        // Check if the addition of this edge (front) will not cause
        // a cycle
        if (!UF.isSameSet(front.second.first, front.second.second))
        {
            mst_cost += front.first;
            UF.unionSet(front.second.first, front.second.second);
        }
    }
}
};
{% endhighlight %}

## Variations

### Maximum Spanning Tree
Instead of sorting the Edge List in ascending order, we sort the Edge List
in descending order and proceed normally.

### Minimum 'Spanning Forest'
In this variant, we want to form a forest of `K` connected components.

Remember, when we first initialize our `UnionFind` class, we have `V` connected components
(none of our nodes are connected initially). Also, whenever we do a union operation
`unionSet()` on two sets, the number of connected components naturally decreases by 1 for
each union.

Hence, to find the Minimum Spanning Forest, we run Krushkal's Algorithm as per normal, 
but as soon as the number of connected components equals to the desired pre-determined number `K`,
we can terminate the algorithm.
{% highlight c++ %}
//Note the UF.numDisjoinSets() > K condition
for (int i = 0; i < EdgeList.size() && UF.numDisjoinSets() > K; i++)
    {
        auto front = EdgeList[i];
        if (!UF.isSameSet(front.second.first, front.second.second))
        {
            mst_cost += front.first;
            UF.unionSet(front.second.first, front.second.second);
        }
    }
{% endhighlight %}

### Second Best Spanning Tree

Sometimes, we may not just want the MST, but also the second best MST of a graph.

To accomplish this, sort the edges then find the optimal MST using Kruskal. Next, for each edge in the MST
(there are at most `V-1` edges in the MST), temporarily flag it so that it cannot be chosen,
then try to find the MST again but now *excluding* that flagged edge.

**Note:** We do not have to re-sort the edges at this point.
 
The best spanning tree found after this process is the second best ST.

{% highlight c++ %}

// We run Kruskal as we always do, nothing unusual here
for (int i = 0; i < E; i++)
{
    auto front = EdgeList[i];
    if (!UF.isSameSet(front.second.first, front.second.second))
    {
        best_result += front.first;
        flag_vector.push_back(i);       // push the edges we select for the 
                                        // MST to a flag vector
        UF.unionSet(front.second.first, front.second.second);
    }
}

// For each edge in the flag vector, re-calculate the MST without it.
for (int i = 0; i < flag_vector.size(); i++)
{
    int skip = flag_vector[i];
    int temp_result = 0;
    UF.reset();
    
    // Re-doing Kruskal, skipping over the current flagged edge
    for (int j = 0; j < E; j++)
    {
        auto front = EdgeList[j];
        if (j != skip && !UF.isSameSet(front.second.first, 
                                       front.second.second))
        {
            temp_result += front.first;
            UF.unionSet(front.second.first, front.second.second);
        }
    }
    
    // IMPORTANT
    // Check that we are still left with a MST that connects all of the 
    // nodes together.
    
    // This is to avoid a case where the flagged edge is a Bridge, 
    // resulting in a disconnected graph, which is definetly
    // not what we want.
    
    if(UF.numDisjoinSets() == 1)
        second_best_result = min(second_best_result, temp_result);
}
{% endhighlight %}

### Minimax and Maximin

The Minimax path problem is the problem of finding the maximum edge weight
along a minumum path.

For example, when traversing through a city roads (edges),
where each road has an associated weight related to sound pollution, we want to
traverse the city such that weight of the maximal edge is minimal.

The Maximin path problem is the opposite. We want the find the minimum edge along
a maximum path.

To solve this problem, we run Kruskal's algorithm on our graph and acquire our
MST. The minimax path solution is thus the max edge weight along the unique
path between vertex i and j in this MST.

{% highlight c++ %}
void dfs(int u, int v_end, int max_val)
{
    if (u == v_end)
        max_w = max_val;
    visited[u] = true;
    for (int i = 0; i < AdjList[u].size(); i++)
    {
        auto v = AdjList[u][i];
        if (!visited[v.second])
            dfs(v.second, v_end, max(max_val, v.first));
    }
}

int main()
{
    vector<bool> visited;
    int maw_w;
    
    sort(EdgeList.begin(), EdgeList.end());
    UnionFind UF(V);
    
    // We run kruskal as usual
    for (int i = 0; i < E; i++)
    {
        auto front = EdgeList[i];
        int u = front.second.first;
        int v = front.second.second;
        if (!UF.isSameSet(u, v))
            UF.unionSet(u, v);
    }
    
    for (int i = 0; i < Q; i++)
    {
        visited.clear();
        visited.resize(V, false);
        max_w = 0;
        int u, v;
        cin >> u >> v;
        dfs(u - 1, v - 1, 0);
        if (max_w == 0)
            cout << "no path" << endl;
        else
            cout << max_w << endl;
    }
}
{% endhighlight %}

