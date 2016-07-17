---
layout: post
title:  "Longest Increasing Subsequence"
category: Notes
---

## Problem Statement

Given a sequence of N numbers, find the longest increasing subsequence
(it's as simple as that). 

**Example:** The length of the LIS of [10, 22, 9, 33, 21, 50, 41, 60, 80]
is *6*, and the LIS itself is [**10**, **22**, 9, **33**, 21, **50**, 41, **60**, **80**]
$$ \rightarrow $$ **[10, 22, 33, 50, 60, 80]**

## General Idea 1 | $$ O(n^2) $$

This solution consists of 2 arrays:

- `LIS[N]` $$ \rightarrow $$ Stores the length of the LIS ending at element `i`
- `previous[N]` $$ \rightarrow $$ Back-reference to previous element
of the LIS ending at `i`

The gist of the solution is to fill up the `LIS` array, it's *that* simple.
 
To accomplish this, we loop over each element `i` in the original sequence.
Now, for each element `j` that came before `i` (as in lower in index than `i`),
we check if both `array[j] < array[i]` and `LIS[j] + 1 > LIS[i]` are satisfied.

- `array[j] < array[i]` needs to be true if we are looking for the longest
**increasing** subsequence. This condition can be changed to `array[j] > array[i]` if
we were looking for the longest **decreasing** subsequence.
- `LIS[j] + 1 > LIS[i]` needs to be true if we are looking for the **longest**
increasing subsequence *i.e* is the sequence coming from `j` longer than the current
sequence ending at `i` ?

Using these two conditions, we're able to fill up the `LIS` array pretty easily.
The maximum length of the LIS will be the maximum value contained in the `LIS` array.

But that's not it. Using the `previous` array, we're also to rebuild the sequence
itself. Check out the code below for details on how we can accomplish this.

To recap, we use the `LIS` array to find the length of the longest subsequence
and the `previous` array to find the sequence itself.

## Code

C++ code below

{% highlight c++ %}
#include<cstdio>
#include <vector>

using namespace std;

const int N = 5;
int array[N] = {1, 0, 2, -5, 5};

int LIS[N]{0};
int previous[N]{0};

vector<int> result;  //A stack is more suitable here

int main()
{
    int maxLength = 1, bestEnd = 0;


    LIS[0] = 1;         // LIS[0] is always 1
    previous[0] = -1;   // index 0 has no element before it

    for (int i = 1; i < N; i++)
    {
        // Always possible to have a subsequence with length of 1
        LIS[i] = 1;     
        // But in this case there will be no back reference
        previous[i] = -1;
        
        // Loop over each elemet previous to i
        for (int j = 0; j < i; j++)
            if ( DP[j] + 1 > DP[i] && array[j] < array[i] )
            {
                DP[i] = DP[j] + 1;
                previous[i] = j;
            }

        // If current LIS > MAXIMUM LIS,
        // we save the index and update maxLength
        if ( DP[i] > maxLength )
        {
            bestEnd = i;
            maxLength = DP[i];
        }
    }

    // To rebuild the LIS, we use the 'previous' array
    int temp = bestEnd;
    while (temp > -1)
    {
        result.push_back(array[temp]);
        temp = previous[temp];
    }
    
    // Backwards loop can be avoid by using a stack
    for (int i = (int) result.size() - 1; i > -1; i--)
    {
        printf("%d ", result[i]);
    }

}
{% endhighlight %}

## General Idea 2 | $$ O(n \space logn) $$

This one is much shorter, faster, but harder to wrap your head around.

[Geeksforgeeks](http://www.geeksforgeeks.org/longest-monotonically-increasing-subsequence-size-n-log-n/)
does a much better job at explaining it than I will ever do, so hop on over there
if you want the detailed explanation of the logic behind it.

## Code

C++ code below

{% highlight c++ %}
#include <iostream>
#include <algorithm>

using namespace std;

const int n = 10;
int A[n] = {5, 19, 5, 81, 50, 28, 29, 1, 83, 23};
int loc_to_place;
vector<int> DP;

int main()
{
    for (int i = 0; i < n; i++)
    {
        // Get iterator to where A[i] should be placed in DP
        // -DP.begin() to get the actual index instead of iterator
        loc_to_place = lower_bound(DP.begin(), DP.end(), A[i]) - DP.begin();

        // If lower_bound returns DP.size()
        // i.e A[i] is bigger than everything in DP append A[i] to DP
        if ( loc_to_place >= DP.size() )
            DP.push_back(A[i]);

        // else if A[i] has found another integer to replace
        // it will place itself in DP[i]
        else
            DP[loc_to_place] = A[i];
    }


    // DP SIZE CONTAINS THE LENGTH OF THE LARGEST SUBSEQUENCE
    // NOT THE SUBSEQUENCE ITSELF
    DP.size();
}
{% endhighlight %}