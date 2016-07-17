---
layout: post
title:  "Modulo for negative numbers in C++"
category: Notes
---

## Problem Statement

Say we want to implement a circular array. Usually,
circular arrays are implemented using the `%` operator.

Now, let `A` be an array of length 4. Say we're at `A[3]` and we want to circle back
to `A[0]`. Well, that's easy. Add 1 to our current index, 3, and modulo it with 4 and we're
back to `A[0]`. In code, `4 % 4 = 0` is always true.

But what happens when we want to circle back from the other direction? What happens
if we're at `A[0]` want to circle back to `A[3]`? Is `-1 % 4 == 0`? Well, if you're using
C or C++, **no**.

## General Idea

The `%` operator in C/C++ is not actually defined as modulo operator. It's defined
as the *remainder* operator.

`-1 % 4` in C/C++ is actually equal to `-1`, not `3` as we'd expect.

The solution? Well, either use a programming language that actually implements
the modulo operator, like Python, or use the code snippet below.

## Code

C++ code below.

{% highlight c++ %}
long long mod(long long k, long long n)
{
    return ( ( k % n ) + n ) % n;
}
{% endhighlight %}

_**Note:** The second `%` in the above snippet incurs heavy performance 
drops over a large data set. The snippet below is more optimized._

{% highlight c++ %}
long long mod(long long k, long long n)
{
    return ((k %= n) < 0) ? k+n : k;
}
{% endhighlight %}