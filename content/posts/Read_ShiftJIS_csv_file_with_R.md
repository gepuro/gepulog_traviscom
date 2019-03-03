---
title: Shift-JISなcsvファイルをRで読み込む
date: 2013-06-15T14:53:17+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/38
---

タイトル通りです。
<pre>
filename <- file("data.csv",encoding="Shift-JIS")
df <- read.csv(filename,,header=T,as.is=T)
</pre>

一行で書ける方法はないのかなあ？

