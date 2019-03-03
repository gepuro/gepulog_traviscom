---
title: xargsまたはparalellを用いてＲを並列処理させる
date: 2014-12-11T17:26:38+09:00
draft: false
tags: [R言語, shellscript]
aliases:
    - /archives/109
---

list.txtから入力を受け取り、hoge.Rで並列処理をさせる。

### hoge.R
~~~~{.r}
argv <- commandArgs(TRUE)
argv <- strsplit(argv, ",")[[1]]
par1 <- as.numeric(argv[1])
par2 <- as.numeric(argv[2])
print(par1)
print(par2)
~~~~

### list.txt
~~~~{.text}
1,1
1,2
2,1
2,2
~~~~

### xargsを用いる場合
~~~~{.sh}
cat list.txt | xargs -P8 -n1 Rscript hoge.R
~~~~

### paralellを用いる場合
~~~~{.sh}
cat list.txt | parallel -j 8 Rscript hoge.R {}
~~~~


