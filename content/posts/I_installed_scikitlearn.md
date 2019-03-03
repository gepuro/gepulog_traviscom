---
title: scikit-learnのインストールをした
date: 2013-02-28T02:42:02+09:00
draft: false
tags: [Python, scikit-learn]
aliases:
    - /archives/20
---

Debian系のOSを使っている人は、
<pre>
$ sudo apt-get install python-sklearn
</pre>
でインストール出来る。

最近、scikit-learnが流行って来ているようなので、少しずつ使っていきたいな。

まずは、インストールの確認ということで、
<pre>
$ ipython
Python 2.7.3 (default, Sep 26 2012, 21:51:14) 
Type "copyright", "credits" or "license" for more information.

IPython 0.13.1.rc2 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: import sklearn

In [2]: sklearn.\_\_version\_\_
Out[2]: '0.11'
</pre>

インストールは無事に出来ているようです。

最新版は0.13.1なので、一部動かないものがあるかもしれない。

