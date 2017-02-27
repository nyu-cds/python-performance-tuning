---
title: "Introduction to Profilng"
teaching: 10
exercises: 0
questions:
- "What does it mean to profile a program?"
- "What are the different types of profiling techniques?"
- "How can profiling information be used?"
objectives:
- "Learn about different ways to profile a program."
- "Understand how profile information can be used to improve a program."
keypoints:
- "Profiling provides useful information on where to focus optimization efforts."
- "Different techniques provide a variety of useful information."
- "Profiling can have an impact on the performance of the program."
---
In order to optimize a program, it is essential to understand where the bottlenecks are. These are the places where the program is spending most of 
its time. A *profile* is a set of statistics that describes how often and for how long various parts of the program executed.

*Deterministic profiling* is meant to reflect the fact that all function call, function return, and exception events are monitored, and precise 
timings are made for the intervals between these events (during which time the user’s code is executing). Deterministic profiles are obtined
by *instrumenting* the program - inserting intructions into the program that collect this timing information.

*Statistical profiling* is a process that randomly samples the effective instruction pointer, and deduces where time is being spent. This technique 
traditionally involves less overhead as the code does not need to be instrumented, but provides only relative indications of where time is being 
spent.

In Python, there is an interpreter active during execution, so the presence of instrumented code is not required to do deterministic profiling. 
Python automatically provides a hook (optional callback) for each event. In addition, the interpreted nature of Python tends to add so much 
overhead to execution, that deterministic profiling only adds small processing overhead in typical applications. The result is that deterministic 
profiling is not expensive, yet provides extensive run time statistics about the execution of a Python program.

Call counts (i.e. how many times a function is called) and profiling statistics can be used for a variety of purposes:

1. Unusual count numbers can help identify bugs in code.
2. High call counts can help to identify possible points where in-lining (unwrapping loops) might benefit.
3. Internal time statistics can be used to identify “hot loops” that should be carefully optimized.
4. Cumulative time statistics can be used to identify high level errors in the selection of algorithms.

Python has two standard modules that provide the same profiling interface: *cProfile* and *profile* (a third, called hotshot, is no longer maintained). 
The cProfile module has the lowest overhead, but because it is written in C, may not be as widely available. The profile module is written in 
Python, so has a much higher overhead, but is easier to extend. There is also a newer module, called *line_profiler* that profiles on a line-by-line 
basis. This module is not part of the standard distribution, so needs to be installed separately.