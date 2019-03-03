---
title: Python3で「if a in b」をするときはsetを使うべし
date: 2016-07-08T08:17:07+09:00
draft: false
tags: [Python3, ベンチマーク]
aliases:
    - /archives/154
---

タイトルの通りです。下のコードで確認しました。
データが増えてくると1万倍は変わるようです。

~~~
Python 3.5.1 |Anaconda 2.4.1 (64-bit)| (default, Dec  7 2015, 15:00:12) [MSC v.1900 64 bit (AMD64)]
Type "copyright", "credits" or "license" for more information.

IPython 4.0.1 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: %cpaste
Pasting code; enter '--' alone on the line to stop or use Ctrl-D.
:import time
:
:a = [i for i in range(10000)]
:b = [i for i in range(5000,100000)]
:start = time.time()
:[i for i in a if i not in b]
:elapsed_time = time.time() - start
:print(elapsed_time)
:
:a = [i for i in range(10000)]
:b = set([i for i in range(5000,100000)])
:start = time.time()
:[i for i in a if i not in b]
:elapsed_time = time.time() - start
:print(elapsed_time)
:<EOF>
7.634490013122559
0.0005004405975341797
~~~

