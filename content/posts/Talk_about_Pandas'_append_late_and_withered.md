---
title: Pandasのappendが遅くて萎えた話
date: 2016-07-06T21:49:06+09:00
draft: false
tags: [Python3, ベンチマーク]
aliases:
    - /archives/152
---

あまりにも処理に時間がかかるので、下のコードで確認した。

~~~{Python}
gepruo@ubuntu$ ipython3
Python 3.5.1+ (default, Mar 30 2016, 22:46:26) 
Type "copyright", "credits" or "license" for more information.

IPython 2.4.1 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: %cpaste
Pasting code; enter '--' alone on the line to stop or use Ctrl-D.
:import pandas as pd
:import time
:start = time.time()
:df = pd.DataFrame()
:for i in range(100):
:    df.append(pd.DataFrame([1,2,3]))
:elapsed_time = time.time() - start
:print(elapsed_time)
:
:
:start = time.time()
:def gen_data():
:    for i in range(100):
:        yield [1,2,3]
:df = pd.DataFrame([i for i in gen_data()])
:elapsed_time = time.time() - start
:print(elapsed_time)
:
:<EOF>
0.21839094161987305
0.0008673667907714844
~~~

たった100回のappendであっても約251倍も変わることが分かった。


