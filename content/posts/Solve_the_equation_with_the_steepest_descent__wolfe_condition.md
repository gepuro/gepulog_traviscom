---
title: 最急降下＋wolfe条件で方程式の解を求める
date: 2013-08-10T12:40:54+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/51
---

$100(x_1 - x_2^2)^2 + (x_2 - 1)^2$の解を最急降下法で求める課題が大学の講義で出たのですが、なかなか面白かったので、ブログに残しておきます。
解を求める関数については、[http://www.wolframalpha.com/input/?i=100%28x_1+-+x_2^2%29^2+%2B+%28x_2+-+1%29^2&dataset=](http://www.wolframalpha.com/input/?i=100%28x_1+-+x_2^2%29^2+%2B+%28x_2+-+1%29^2&dataset=)を見れば、良いでしょう。

<pre>
library(MASS)

kansu <- function(x1, x2){
  100*(x1-x2^2)^2 + (x2 - 1)^2
}

nabura <- function(x1, x2){
  nabura1 <- 200*(x1 - x2^2)
  nabura2 <- 2*(200*x2^3 + (1-200*x1)*x2 - 1)
  c(nabura1, nabura2)
}

teisi <- function(x1, x2, epsilon){
  sqrt( sum(kansu(x1, x2)^2) ) <= epsilon
}

woelf <- function(x1, x2, d, beta, guzai1, guzai2){
  nx <- c(x1,x2) + beta * d
  zyoken1 <- kansu(nx[1], nx[2]) <= kansu(x1, x2) + guzai1 * beta * (nabura(x1, x2) %*% d)[1]
  zyoken2 <- guzai2 * (nabura(x1, x2) %*% d)[1] <= (nabura(nx[1], nx[2]) %*% d)[1]
  if(zyoken1 == TRUE && zyoken2 == TRUE){
    return(TRUE)
  }else{
    return(FALSE)
  }
}

kekka <- function(x.history){
  x1 <- seq(-1,3, by=0.01)
  x2 <- seq(-1,3, by=0.01)
  z <- outer(x1,x2,kansu)
  contour(x1,x2,z, nlevels=50)
  # filled.contour(x1,x2,z, nlevels=300)
  points(x.history$x1, x.history$x2, pch=20, cex=0.5)
}


# 条件
epsilon <- 1/100000
guzai1 <- 1/4
guzai2 <- 1/2
tau <- 1/2


# step0
x1 <- 0
x2 <- 0
k <- 0

x.history <- c()
while( teisi(x1, x2, epsilon) == FALSE){ #step1
  d <- - nabura(x1, x2) # step2
 
  # step3
  beta <- 1
  i <- 0
  while( woelf(x1, x2, d, beta, guzai1, guzai2) == FALSE ){
    beta <- tau * beta
  }
  alpha <- beta
 
  # step4
  x.history <- rbind(x.history, data.frame(x1,x2))
  x1 <- x1 + alpha * d[1]
  x2 <- x2 + alpha * d[2]
 
  k <- k+1
}
c(x1,x2)
kekka(x.history)
</pre>

結果は、(x1,x2)=(0.9937275, 0.9968645)となった。真の解は、(x1,x2)=(1,1)であるから、精度よく求められていることが分かる。

収束する様子は、次のようになっている。

<img src="http://img.gepuro.net/image/51.png">


