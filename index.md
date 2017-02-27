---
layout: lesson
root: .
---
Python interpreter implementationsm, such as CPython, attempt to optimize the performance of the running program. However the
nature of the Python language can make this a challenging task. Dynamic types and other Python features prevent the language from
being statically optimized, so there are many tasks that can only be performed when the program runs. In addition, Python data
types can be inefficient if used incorrectly, so it is important to ensure that the right data structure is used for the job.

In this lesson, we will look at how to diagnose and solve performance related issues in Python programs.

> ## Prerequisites
>
> The examples in this lesson can be run directly using the Python interpreter, using IPython interactively, 
> or using Jupyter notebooks.
{: .prereq}

