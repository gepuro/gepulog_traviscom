---
title: 指数分布の確率プロット
date: 2015-01-26T22:12:13+09:00
draft: false
tags: [R言語, 統計]
aliases:
    - /archives/117
---

2年ぐらい前にお勉強用に書いたコードを再利用するために発掘したので、共有しておきます。
"exp"の箇所を好きな分布に書きかえれば流用できるかと思います。

~~~{.r}
exponential.plot <- function(x){
  qq <- function(x){
    n <- length(x)
    qexp((1:n - 1/2)/n, rate=mean(x))[order(order(x))] # ミラーランク
  }
  
  probs<- c(0.001, 0.01, 0.1, 1, 5, 10, 20, 30, 50, 70, 80, 90, 95, 99, 99.9, 99.99, 99.999) / 100
  
  plot(c(min(x), max(x)),  qexp(c(probs[1], probs[length(probs)])), type="n", xaxt="n", yaxt="n", 
       ylab="Cumulative Percent", xlab="Observed Value")
  abline(h=qexp(probs), v=as.integer(seq(min(x), max(x))), col="grey")
  abline(h=qexp(0.5))
  axis(side=2, at=qexp(probs[c(4,8:17)]), las=1, labels=as.character(probs[c(4,8:17)]*100), cex.axis=0.8)
  axis(side=1, as.integer(seq(min(x), max(x))), cex.axis=0.8)
  points(x, qq(x))
  
  model <- lm(qq(x) ~ x - 1)
  abline(model)
  print(paste("r-squared",summary(model)$r.squared,sep=":"))
  # Maximum likelihood estimation
  print(paste("rate",mean(x),sep=":"))
}

rexp.data <- rexp(100,rate=1)
exponential.plot(rexp.data)
~~~


出力結果は、

![exponential_plot](https://www.dropbox.com/s/el8mnconhuhgpuw/exponential_plot.png?dl=1)

となります。


参考

 * [正規確率紙](http://www.kogures.com/hitoshi/webtext/stat-seiki-kakuritushi/)

