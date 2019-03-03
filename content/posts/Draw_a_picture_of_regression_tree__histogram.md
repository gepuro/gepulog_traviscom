---
title: 回帰木＋ヒストグラムの図を描く
date: 2014-12-22T19:22:52+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/114
---

回帰木の下にヒストグラムがあると格好良いと思ったので、メモしておく。

~~~{.r}
library("party")
data(cars)
model <- ctree(dist~., data=cars)
t.style <- node_hist(model, ymax=0.06, xscale=c(0,150), col="red", fill=hsv(0.6, 0.5, 1))
plot(model, terminal_panel = t.style)
~~~


![回帰木](https://www.dropbox.com/s/toau4vq1wdqjp6r/20141222_rpart.jpeg?dl=1)


### 参考

* [Rによるデータサイエンス13「樹木モデル」
](http://www.slideshare.net/takemikami/r13-9821987)


