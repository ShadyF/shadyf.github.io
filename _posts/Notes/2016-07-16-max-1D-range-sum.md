---
layout: post
title:  "Max 1D range sum"
category: Notes
---

## Problem Statement

Given a sequence of N positive or negative numbers, find a contiguous sub-array
whose numbers have the largest sum.

**Example:** The Max 1D range sum of [−2, 1, −3, 4, −1, 2, 1, −5, 4] is
*6* resulting from the [−2, 1, −3, **4, −1, 2, 1**, −5, 4] sub-array.

## General Idea

This problem can be solved using [Kadane's algorithm](https://en.wikipedia.org/wiki/Maximum_subarray_problem#Kadane.27s_algorithm).
The gist of it is to

- Keep track of the current `result` and add each number in the sequence to `sum`
- If `sum > result`, let `result = sum`
- After going through all the numbers, `result` will contain the sum of the sub-array
whose numbers have the largest sum.

The above solution can also be added upon to detect the start and the end
of the sub-array.

**Complexity:** $$ O(n) $$

## Code

C++11 code Below

{% highlight c++ %}

#include <cstdio>
#include <algorithm>

using namespace std;

long long int sum;
long long int result;

int main()
{
    sum = 0;
    result = 0;

    for (int i = 0; i < N; i++) {
        sum += numbers[i];           // Add current number to sum
        result = max(result, sum);   // result is the max of result and sum
        if ( sum < 0 ) sum = 0;      // If sum goes below 0, reset
    }
    
    if ( result <= 0 )
        printf("Losing streak.\n");
    else
        printf("The maximum winning streak is %lld.\n", result);

}

{% endhighlight %}
