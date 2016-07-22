---
layout: post
title:  "UVa | 11747 - Heavy Cycle Edges"
category: Programming Problems
---

## Problem Statement
"Given an undirected graph `G` with edge weights, output all edges that are
the heaviest edge in some cycle of `G`"

[Problem Link](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2847)

## Thought Process

Well, it's a minimum spanning tree problem. Either `Kruskal`'s or
`Prim`'s algorithm should work fine here (`Kruskal`'s is used below).
 
Instead of printing the edges of the minimum spanning tree itself, we should print
the edges that were not chosen by `Kruskal`'s/`Prim`'s algorithm. We can easily
do this by initializing a set, `edges_set`, to contain all the weights of
the graph `G`, and whenever we pick an edge to be part of our minimum spanning
tree, we removed it from the `edges_set`

At the end, we're left with `edges_set` that has all the edges that were
not picked, i.e the heaviest ones. Sort it and print its content.

## Code

#### C++11 code below

{% highlight c++ %}
#include <iostream>
#include <vector>
#include <algorithm>
#include <set>
#include <functional>

using namespace std;

int n, m;
vector<pair<int, pair<int, int>>> EdgeList;
multiset<int> edges_set;

class UnionFind
{
    vector<int> p, rank;

public:
    explicit UnionFind(int N)
    {
        rank.assign(N, 0);
        p.assign(N, 0);
        for (int i = 0; i < N; i++) p[i] = i;
    }

    int findSet(int i)
    { return ( p[i] == i ) ? i : ( p[i] = findSet(p[i]) ); }

    bool isSameSet(int i, int j)
    { return findSet(i) == findSet(j); }

    void unionSet(int i, int j)
    {
        if (!isSameSet(i, j))
        {
            int x = findSet(i), y = findSet(j);
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
    ios_base::sync_with_stdio(false);
    cin.tie(0);
    while (cin >> n >> m && ( n != 0 || m != 0 ))
    {
        EdgeList.clear();
        edges_set.clear();
        for (int i = 0; i < m; i++)
        {
            int u, v, w;
            cin >> u >> v >> w;
            EdgeList.push_back(make_pair(w, make_pair(u, v)));
            edges_set.insert(w);
        }
        sort(EdgeList.begin(), EdgeList.end());
        UnionFind UF(n);
        for (int i = 0; i < m; i++)
        {
            auto front = EdgeList[i];
            if (!UF.isSameSet(front.second.first, front.second.second))
            {
                edges_set.erase(edges_set.find(front.first));
                UF.unionSet(front.second.first, front.second.second);
            }
        }
        if(edges_set.empty())
            cout << "forest";
        else
        {
            for(auto i = edges_set.begin(); i != prev(edges_set.end()); i++)
                cout << *i << " ";
            cout << *prev(edges_set.end());
        }
        cout << endl;
    }
}
{% endhighlight %}

&nbsp;

#### Python3.5 code below

{% highlight python %}
class UnionFind:
    p = rank = []

    def __init__(self, N):
        self.p = [i for i in range(N)]
        self.rank = [0] * N

    def find_set(self, i):
        if self.p[i] == i:
            return i
        else:
            self.p[i] = self.find_set(self.p[i])
            return self.p[i]

    def is_same_set(self, i, j):
        return self.find_set(i) == self.find_set(j)

    def union_set(self, i, j):
        if not self.is_same_set(i, j):
            x = self.find_set(i)
            y = self.find_set(j)

            if self.rank[x] > self.rank[y]:
                self.p[y] = x
            else:
                self.p[x] = y
                if self.rank[x] == self.rank[y]:
                    self.rank[y] += 1

n, m = map(int, input().split())

while n != 0 or m != 0:
    edge_list = []
    edges_set = []

    for i in range(m):
        u, v, w = map(int, input().split())
        edge_list.append((w, u, v))
        edges_set.append(w)

    edge_list.sort(key=lambda edge: edge[0])
    UF = UnionFind(n)

    for i in range(m):
        front = edge_list[i]
        if not UF.is_same_set(front[1], front[2]):
            edges_set.remove(front[0])
            UF.union_set(front[1], front[2])

    if not edges_set:
        print('forest')
    else:
        edges_set.sort()
        for i in edges_set[:-1]:
            print(str(i) + ' ', end='')
        print(edges_set[-1])

    n, m = map(int, input().split())

{% endhighlight %}