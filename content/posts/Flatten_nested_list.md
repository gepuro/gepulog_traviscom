---
title: ネストされたリストを平坦にする
date: 2013-08-29T16:24:03+09:00
draft: false
tags: [Python]
aliases:
    - /archives/57
---

[Python で flatten - ネストしたリストをフラットにする](http://jutememo.blogspot.jp/2008/10/python-flatten.html)を参考にして、タプルが含まれていても、実行出来るようにした。
<pre>
def flatten(L):
    if isinstance(L, (list, tuple)):
        if L == [] or L == ():
            return []
        else:
            return flatten(L[0]) + flatten(L[1:])
    else:
        return [L]
</pre>
isinstanceの箇所を変更しただけです。

