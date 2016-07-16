---
layout: post
title:  "Max 2D range sum"
category: Notes
---

## Problem Statement

Given a 2D array of positive and negative integers, find the sub-rectangle
with the largest sum. 

## General Idea

- Create another 2D array `A`, where `A[i][j]` is the sum of the sub-rectangle
ranging from `(0, 0)` to `(i, j)` - $$ O(n^2) $$

- Calculate the sum of each sub-rectangle - $$ O(n^4) $$

**Complexity:** $$ O(n^2 + n^4) \rightarrow O(n^4) $$

*__Note:__ This is the naive solution. Time complexity can be reduced to
$$ O(n^3) $$ using [this](http://www.geeksforgeeks.org/dynamic-programming-set-27-max-sum-rectangle-in-a-2d-matrix/)
algorithm, which is why I don't delve into the details of my solution.*

## Code

C++ code below.

{% highlight c++ %}
#include <cstdio>
#include <vector>
#include <algorithm>

using namespace std;

int N, temp, sum, result;
vector< vector<int> > A;

int main()
{
  while(scanf("%d", &N) != EOF)
  {
    A.clear();
    A.resize(N);

    // set sum and result to min value
    sum = -127*100*100; result = -127*100*100;

    // Create new matrix A
    for(int i = 0; i < N; i++)
      for(int j = 0; j < N; j++)
      {
        scanf("%d", &temp);
        A[i].push_back(temp);
        
        // Get sum from its top and add it to current value
        if(i > 0) A[i][j] += A[i-1][j];
        
        // Get sum from left and add it to current values
        if(j > 0) A[i][j] += A[i][j-1]; 
        
        // If both left and top wereadded, 
        // remove top left value because it was included
        // when we got sum from left once and again when we got it from top
        if(i > 0 && j > 0) A[i][j] -= A[i-1][j-1];
      }

    // Calcute the sum of each possible box,
    // where (i, j) are starting points and (k, l) are ending points
    
    // EXAMPLE
    // first loop will compute all possible boxes from (0, 0) to (N, N)
    // Next loop from (0, 1) to (N, N) and so on....
    // To compute, for example, from (2, 2) to (5, 6)
    // Take entry of (5, 6) which represents the sum from (0, 0) to (5, 6)
    // Then remove left and top portions i.e 
    // TOP PORTION - remove from (0, 0) to (1, 6)
    // LEFT PORTION - remove from (0, 0) to (5, 1)
    // If removed both top and left portions, add (1, 1) to compensate
    // Check if this sum > result, if true, result = sum
    
    for(int i = 0; i < N; i++) for(int j = 0; j < N; j++)
      for(int k = i; k < N; k++) for(int l = j; l < N; l++)
      {
        sum = A[k][l];
        if(i > 0) sum -= A[i-1][l];
        if(j > 0) sum -= A[k][j-1];
        if(i > 0 && j > 0) sum += A[i-1][j-1];
        result = max(sum, result);
      }
      printf("%d\n", result);
  }
}
{% endhighlight %}
