---
title: コードのハイライティングを出来るようにした
date: 2013-10-16T19:53:38+09:00
draft: false
tags: [product, Python]
aliases:
    - /archives/69
---

Pygmentsを
~~~~
pip install Pygments
~~~~
でインストールして、
~~~~{.python}
markdown.markdown(html, ['fenced_code', 'codehilite'])
~~~~
とすれば良い。
あとは、
 [https://github.com/richleland/pygments-css](https://github.com/richleland/pygments-css) からお気に入りのハイライティングを選んできて、適切な箇所に置く。


以下がサンプル
~~~~
print "Hello World"
~~~~

~~~~{.python}
print "Hello World"
~~~~

~~~~
#!/usr/bin/python
# -*- coding:utf-8 -*-
print "Hello World"
~~~~

参考:

* [http://pythonhosted.org/Markdown/extensions/code_hilite.html](http://pythonhosted.org/Markdown/extensions/code_hilite.html)
* [https://github.com/richleland/pygments-css](https://github.com/richleland/pygments-css) 


#### 追記
markdownは2.1.1、Pygmentsは1.6で動作確認をしている。バージョンが異なると挙動が変わる模様。


