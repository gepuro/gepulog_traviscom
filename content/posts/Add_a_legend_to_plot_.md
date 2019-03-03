---
title: plot()に凡例を追加する
date: 2013-07-29T22:12:15+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/44
---

サンプルコードを載せておく。
<pre>
plot(1:10,1:10, type="l", lty=1)
lines(1:10,seq(1,5.5,by=0.5),lty=2)
legend(locator(1), c("実線","点線"),lty=1:2)
</pre>
出力は、下のようになる。
<img src="http://img.gepuro.net/image/legend_example.png">

locator(1)を使うと、凡例をどこに表示させるかをマウスポインタで選択できるので便利。
また、ltyをcolにすれば、色ごとに表示できる。

(普段良く使うコードをまとめて置くと、忘れた時に楽になると信じてる

