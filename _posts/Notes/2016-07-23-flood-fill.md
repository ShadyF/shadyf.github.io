---
layout: post
title:  "Flood Fill"
category: Notes
---

## Problem Statement

Given a graph, color similarly-colored nodes/areas with the same color.

See this [*Wikipedia article*](https://en.wikipedia.org/wiki/Flood_fill)
for an illustration.

## General Idea | $$ O(V+E) \space or \space O(row+col) $$

The general idea is to select a target/starting node, a target color and
a replacement color. We then recursively loop over the starting node's
neighbours, replacing the target color with the replacement color for each
node.

There are many variations to this algorithm. This algorithm can be run
on a typical graph (with `vertices` and `edges`), or, on an implicit 2D graph,
i.e a 2D array.

There is also a`stack`-based recursive implementation (akin to `DFS`) and another
`queue`-based implementation, based on `BFS`.
 
The disadvantage of using the `stack`-based recursive implementation is that 
it is impractical when the dataset is too large or when the stack space is
severely contrained, leading to a stack overflow.

There are also variation when dealing with an implicit 2D graph. Namely,
8-way flood fill or 4-way flood fill, depending on whether we
should consider nodes touching at corners connected or not.

Pseudocode for all these different variations are in this
[*Wikipedia article*](https://en.wikipedia.org/wiki/Flood_fill#The_algorithm)

## Code

C++11 code for an 8-way flood fill on an implicit 2D graph, using a
stack-based approach.

{% highlight c++ %}

// trick to explore an implicit 2D grid
// S,SE,E,NE,N,NW,W,SW neighbors
int dr[] = {1, 1, 0, -1, -1, -1, 0, 1};
int dc[] = {0, 1, 1, 1, 0, -1, -1, -1};

int floodfill(int r, int c, char color_to_check, char color_to_replace_with)
{
    // If out of bounds
    if (r < 0 || r >= R || c < 0 || c >= C) return 0;
    
    // If not the color we want to replace
    if (grid[r][c] != color_to_check) return 0;
    
    // Recolors node (r, c)
    // This also prevent cycling
    grid[r][c] = color_to_replace_with;    
    
    // ans contains the number of nodes that have been filled
    // Adds 1 to ans because the current node has been filled
    int ans = 1;
    
    // Loop over neighbours, 8-way
    for (int d = 0; d < 8; d++)
        ans += floodfill(r + dr[d], c + dc[d], color_to_check,
                         color_to_replace_with);
     
    return ans;
}

// Example usage in main
int main()
{
    for (int i = 0; i < M; i++)
        for (int j = 0; j < N; j++)
            if (grid[i][j] == color_to_check)
            {
                current_count = floodfill(i, j, color_to_check,
                                         color_to_replace_with);
                                              
                if(current_count > max_count)
                    max_count = current_count;
            }
}
{% endhighlight %}