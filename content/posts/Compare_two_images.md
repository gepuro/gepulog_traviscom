---
title: 2つの画像を比較
date: 2015-10-09T01:38:54+09:00
draft: false
tags: [Python3]
aliases:
    - /archives/134
---

<p>Pythonで２つの画像の比較をしたい。</p>
<p>シェル上で実行するなら、</p>
<pre class="{text}"><code>diff img1.png img2.png</code></pre>
<p>で良い。</p>
<p>Pythonでは、</p>
<pre class="{text}"><code>img1.tostring() == img2.tostring()</code></pre>
<p>で比較出来る。</p>
もっと綺麗な方法がある気がする。その時は加筆する。


