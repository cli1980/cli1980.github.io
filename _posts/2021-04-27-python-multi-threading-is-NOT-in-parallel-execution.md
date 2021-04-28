---
layout: single
title: "Python Multi-threading IS NOT in Parallel Execution"
categories: Note
tags:
  - Python
  - Multi-threading
  - Parallel Execution
published: true
---
I did not realize that Python threading is not in parallel execution until my coworker asked me to check the GIL after I complained about a freeze of the entire GUI caused by a network fetching call, which is truly embarrassing. After googling several articles that detail the infamous GIL: [What Is the Python Global Interpreter Lock (GIL)?](https://realpython.com/python-gil/) and [Introduction to the Infamous Python GIL](https://granulate.io/introduction-to-the-infamous-python-gil/), I finally got the truth: Python uses a global mutex in its interpreter to protect the reference count of each object from being messed up among multiple threads, which is called Global Interpreter Lock, aka GIL; They chose to use a single lock just because a lock to each object could cause deadlocks(is it so called pythonic? I don't know); And Yes, the GIL brings performance penalty to CPU bound programs in multi-threading since they will actually be hyper-threaded in one CPU core; As for why not removing the GIL given plenty of complaints from the developers, the answer seems to be "as such a popular language Python cannot bring a change as significant as removal of GIL without causing backward incompatibility issues"[^1].

Are there any solutions to overcome the problems caused by GIL? Possibly.
- To use [the Python Multi-Processing Package](https://docs.python.org/3/library/multiprocessing.html) for your CPU bound programs and be prepared for the issues caused by [pickling](https://docs.python.org/3/library/pickle.html)
- Try alternative Python implementations such as [PyPy](https://doc.pypy.org/en/latest/index.html), however keep in mind that not all your dependent packages will be supported there.
- If you implement a python module through C++ binding and there happens to be some blocking operations, remember to give back the GIL at the start and acquire it again at the end. (This probably is not a solution but a fix after you find one of your call blocks the entire program, which is exactly what I got today)

Is GIL so bad? Maybe not, they claimed you'll get a performance boost with it for your single thread only program.

What?

[^1]: [What Is the Python Global Interpreter Lock (GIL)?](https://realpython.com/python-gil/)
