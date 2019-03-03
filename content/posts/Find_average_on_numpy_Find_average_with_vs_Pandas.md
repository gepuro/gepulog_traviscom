---
title: numpy上で平均を求める v.s. Pandasで平均を求める
date: 2016-07-07T11:33:25+09:00
draft: false
tags: [Python3, ベンチマーク]
aliases:
    - /archives/153
---

計算の待ち時間が長く感じることが増えたので調べる
結論は、numpyのみで計算した方が早い。
以下で確認した。

~~~
Python 3.5.1+ (default, Mar 30 2016, 22:46:26) 
Type "copyright", "credits" or "license" for more information.

IPython 2.4.1 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: %cpaste
Pasting code; enter '--' alone on the line to stop or use Ctrl-D.
:import numpy as np
:import time
:
:start = time.time()
:dt = np.array([0 for i in range(100)], dtype=float)
:for i in range(100):
:    dt += dt
:dt = dt/100
:elapsed_time = time.time() - start
:print(elapsed_time)
:
:import pandas as pd
:start = time.time()
:dt = np.array([0 for i in range(100)], dtype=float)
:dt_list = [dt for i in range(100)]
:pd.DataFrame(dt_list).mean()
:elapsed_time = time.time() - start
:print(elapsed_time)
:<EOF>
0.00039386749267578125
0.00755763053894043
~~~

