---
title: plot()を使う時の軸をいじる
date: 2013-06-25T16:30:20+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/39
---

サンプルコードを載せておく
<pre>
plot(1:10,1:10,axes=F)
axis(1, at=(0, 10, by=2), las=2, cex.axis=0.7) # 下側、縦書き、フォント小さめ
axis(2, at=seq(0,10, by=5), las=1, cex.axis=0.7) #左側、横書き、フォント小さめ
box() # 線で図を囲む
</pre>

