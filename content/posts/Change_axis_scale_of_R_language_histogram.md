---
title: R言語のヒストグラムの軸目盛を変える
date: 2013-07-08T18:33:09+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/40
---

hist()でヒストグラムを書く時に軸の目盛を変える方法を忘れるのでメモ

<pre>
bpoint <- seq(1, 100, by=10)
hist(df$hoge, axes=FALSE)
axis(1, at = bpoint, labels = bpoint)
axis(2)
</pre>

