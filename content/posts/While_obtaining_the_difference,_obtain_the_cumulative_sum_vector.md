---
title: 差分を求めながら、累積和のベクトルを得る
date: 2013-10-16T18:26:38+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/67
---

値が戻る時は、リセットする。

よく使う気がするので、関数にしておく。
~~~~{.R}
cumsum2 <- function(x){
  mae <- x[1]
  tmp <- c(mae)
  for(i in 2:length(x)){
    if(mae > x[i]){
      tmp <- c(tmp, tmp[length(tmp)] + x[i])
    }else{
      tmp <- c(tmp, tmp[length(tmp)] + (x[i]-mae))
    }
    mae <- x[i]
  }
  tmp
}
~~~~

