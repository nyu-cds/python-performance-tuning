---
title: "line_profiler"
teaching: 30
exercises: 10
questions:
- "What information does `line_profiler` provide?"
- "How is `line_profiler` different from `cProfile`?"
objectives:
- "Learn how to use `line_profiler` to profile a program."
- "Learn how to interpret the output of `line_profiler`."
keypoints:
- "`line_profiler` generates profiles based on the lines in a program."
- "Line based profiling can provide more information about where a program is performing badly."
---
The `cProfile` module shows how much time is being spent in each function. This is a good first step for locating hotspots in a program, and is 
frequently all that is needed to optimize the code. However, sometimes the cause of the hotspot is actually a single line in the function, and 
that line may not be obvious from just reading the source code. 

These cases are particularly frequent in scientific computing. Functions tend to be larger (sometimes because of legitimate algorithmic 
complexity, sometimes because the programmer is still trying to write FORTRAN code), and a single statement without function calls can 
trigger lots of computation when using libraries like NumPy. 

`cProfile` only times explicit function calls, not special methods called because of syntax. Consequently, a relatively slow NumPy operation 
on large arrays like this:

~~~
a[large_index_array] = some_other_large_array
~~~
{: .python}

is a hotspot that never gets explicitly shown by cProfile because there is no function call in that statement.

The `line_profiler` can be given functions to profile, and it will time the execution of each individual line inside those functions. 
In a typical workflow, it is only useful to examine the line timings of a few functions because wading through the results of timing 
every single line of code would be overwhelming.

The *line profiler* can be used from IPython by loading the `line_profiler` extension. Run the following command to load the extension:

~~~
%load_ext line_profiler
~~~
{: .python}

The following code computes the list of prime numbers up to and including *n* using the *Sieve of Eratosthenes*, which can be expressed 
in pseudocode as follows:

**Input**: an integer *n* > 1

Let *A* be an array of Boolean values, indexed by integers 2 to *n*, initially all set to **true**.

~~~
for i = 2, 3, 4, ..., not exceeding sqrt(n):
 if A[i] is true:
  for j = i**2, i**2+i, i**2+2i, i**2+3i, ..., not exceeding n:
    A[j] := false
~~~
{: .code}

**Output**: all i such that A[i] is true.

Here is an example implementation in Python:

~~~
def primes(n=1000): 
    A = [True] * (n+1)
    A[0] = False
    A[1] = False
    for i in range(2, int(n**0.5)):
        if A[i]:
            for j in range(i**2, n+1, i):
                A[j] = False

    return [x for x in range(2, n) if A[x]]
~~~
{: .python}

Once you have loaded this function, you can profile it with the line profiler using the following command. The "-f" option tells `%lprun` which 
function to profile.

~~~
%lprun -f primes primes(10000)
~~~
{: .python}

Here is an example of the output you should see:

~~~
Timer unit: 1e-06 s

Total time: 0.019715 s
File: <ipython-input-28-0ccce2a0061a>
Function: primes at line 1

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
     1                                           def primes(n=1000): 
     2         1           54     54.0      0.3      A = [True] * (n+1)
     3         1            1      1.0      0.0      A[0] = False
     4         1            1      1.0      0.0      A[1] = False
     5        99           49      0.5      0.2      for i in range(2, int(n**0.5)):
     6        98           49      0.5      0.2          if A[i]:
     7     17006         8956      0.5     45.4              for j in range(i**2, n+1, i):
     8     16981         9233      0.5     46.8                  A[j] = False
     9                                           
    10         1         1372   1372.0      7.0      return [x for x in range(2, n) if A[x]]
~~~
{: .output}

The line profiler will display some information about the execution, including the line "Timer unit:" which gives the conversion factor to 
seconds for time information. It then shows a table with the following column headings (from left to right):

* `Line #` - The line number in the code
* `Hits` - The number of times that line was executed
* `Time` - The total amount of time spent executing the line in the timer's units
* `Per Hit` - The average amount of time spent executing the line once in the timer's unit
* `% Time` - The percentage of time spent on that line relative to the total amount of recorded time spent in the function
* `Line Contents` - The actual source code of the line

From the output, you can see that most of the time was spent at lines 7 and 8, and also at line 10. If we are to improve the performance 
of this function, then we should focus on these lines.

As we have seen previously, one way of improving performance is to use builtin functions rather than Python code. Another way is to use a fast 
library such as NumPy to replace Python code.

> ## Challenge
> Using one or more of these techniques, write a new function called `faster_primes` that achieves better performance the the original function. 
>
> Use the following test code to check the results:
>
> ~~~
> from nose.tools import assert_equal, assert_less
> import timeit
> assert_equal(primes(1000), faster_primes(1000))
> assert_less(timeit.timeit(faster_primes), timeit.timeit(primes))
> ~~~
> {: .python}
{: .challenge}