---
layout: post
title:  "Topological Sorting"
category: Notes
---

## Problem Statement

Given a graph with `V` vertices and `E` edges, sort the vertices so that for
each directed edge, $$ u  \rightarrow  v $$, `u` comes before `v` in ordering.

**Examples** 

- Sorting a sequence of jobs or tasks based on their dependencies. Topological Sorting
gives us an order in which to perform the jobs.
- Sorting logic gates when performing logic synthesis or static timing
analysis.

*__Note 1:__ Topological Sorting can only be applied when the graph has no
directed cycles, i.e can only be applied on DAGs (Directed Acyclic Graph).*

*__Note 2:__ A graph can contain multiple valid topological sorts.*

## General Idea 1 | $$ O(V+E) $$

The first and easiest method to acquire one of the topological sorts of a
graph is using a modified version of `DFS`.

Like `DFS`, this algorithm loops through each node of the graph, in an _arbitrary_
order. After visiting each of its neighbours, the current node gets appended
to a stack, `TopSort`. At the end, the `TopSort` stack will contain the 
topologically sorted nodes.

*__Note:__ The DFS method returns an arbitrary topologically sorted ordering
of the nodes. In other words, we have no control over which of the topological
sorts of the graph is returned. Check out Kahn's algorithm below for a possible
solution to this.*

## Code
C++11 code below

{% highlight c++ %}
stack<int> TopSort;

void topologicalSort(int u)
{
    if (u > n)
        return;
    visited[u] = true;
    for (int i = 0; i < adj_list[u].size(); i++)
        if (!visited[adj_list[u][i]])
            topologicalSort(adj_list[u][i]);

    // Only difference between this and DFS is this single line
    // The last vertex in the DFS sequence i.e the deepest one is added.
    TopSort.push(u);

}

int main()
{
    for (int i = 0; i < m; i++)
    {
        scanf("%d %d", &u, &v);
        adj_list[u].push_back(v);
    }
    for (int i = 0; i < n; i++)
    //Makes sure to get topsort of entire graph even if it is disconnected
        if (!visited[i])
            topologicalSort(i);
            
    // Vertices at the top of the stack are the ones that are "shallow".
    // the more you pop the deeper you go
    while (!TopSort.empty())
    {
        printf(" %d", TopSort.top());
        TopSort.pop();
    }
}

{% endhighlight %}

## General Idea 2 | $$ O(V+E) $$

The second algorithm, [Kahn's algorithm](https://en.wikipedia.org/wiki/Topological_sorting#Kahn.27s_algorithm),
is somewhat similar to `BFS`.

Kahn's algorithm works by first finding the sets of nodes with no incoming
edges (in degree == 0). These nodes will be put in a priority queue. The priority
queue will represent nodes which do not have any current dependencies.

Then, for each node in the priority queue, we'll pop the current node and add it
a list/vector which will contain our topologically sorted graph at the end.
Whenever we pop a node, we'll removed all of its edges aswell and check if
the removal of such edges results in other nodes having in degress of 0.
If yes, push these nodes into the priority queue.

We'll keep on repeating this process until there are no more nodes left 
to process in the priority queue.

*__Note:__ Unlike the DFS method above, we can manipulate which topological
sort we want by varying the priority queue's function. See code below for
an example.*

## Code
C++11 code below

{% highlight c++ %}
// Example main usage

// replace std::greater with whichever **class** you want
// The custom class must have an overriden operator() to work
// as a comparison class
priorirty_queue<int, vector<int>, std::greater<int> > queue;

for (int i = 0; i < M; i++)
{
    cin >> u >> v;
    adj_list[u].push_back(v);
    in_degree[v]++;
}

//enqueue vertices with zero incoming degree into a priority queue
for (int i = 0; i < N; i++)
{
    if (in_degree[i] == 0)
        queue.push(i);
}

while (!queue.empty())
{
    // put vertex u (current_node) into a topological sort list;
    int current_node = queue.top();
    answer.push_back(current_node);
    
    // remove this vertex u (current_node)
    queue.pop();
    for (int i = 0; i < adj_list[current_node].size(); i++)
    {
        int v = adj_list[current_node][i];
        
        // remove all outgoing edges from this vertex u (curent_node)
        in_degree[v]--;

        // if such removal causes vertex v to have zero incoming degree
        // Enqueue it
        if (in_degree[v] == 0)
            queue.push(v]);       
    }
}
{% endhighlight %}