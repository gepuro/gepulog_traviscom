---
title: Rmarkdownのioslidesで右下に画像を配置する方法
date: 2015-11-26T15:04:37+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/137
---

もう少し綺麗な方法があると思うけど、下で出来た。

~~~{R}
<style>
.bottom_right img {
    position: relative;
    left: 500px;
    top: 180px;
}</style>
<div class="bottom_right" markdown="1">
<img src="logo.png", width="300px">
</div>
~~~

kazutanさんに教えてもらった方法
~~~{R}
<aside class="gdbar right bottom"><img src="hoge.png"></aside>
~~~
ありがとうございます！


