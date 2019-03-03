---
title: 最適化計算のメモ（最小値や最大値を求める）
date: 2014-04-14T11:23:43+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/83
---

~~~{code}
hoge <- function(x){
  x1 <- x[1]
  x2 <- x[2]
  x1^2 + x2^2
}
optim(c(1,1), hoge)
~~~
のように実行すれば、最小値を計算できる。最大値を計算するときは、
~~~{code}
optim(c(1,1), hoge, control=list(fnscale=-1))
~~~
のようにする。

