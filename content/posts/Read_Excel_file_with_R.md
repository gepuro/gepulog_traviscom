---
title: Rでエクセルファイルを読み込む
date: 2013-06-13T15:49:33+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/37
---

R言語で、エクセルファイルを読み込むときのメモ

<pre>
install.packages("xlsx", dep=T)
library(xlsx)
df <- read.xlsx("data.xlsx", sheetName="Sheet1")
</pre>

csvファイルを読み込むときに比べて、かなり遅いような気がする。

