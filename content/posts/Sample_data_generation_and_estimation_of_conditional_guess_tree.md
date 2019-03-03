---
title: 条件付き推測木のサンプルデータ生成と推定
date: 2014-12-03T01:56:35+09:00
draft: false
tags: [R言語, 統計]
aliases:
    - /archives/103
---

## 条件付き推測木（決定木）で生存時間解析

寿命分布が異なるように説明変数で分割する。

詳細は[http://cran.r-project.org/web/packages/party/party.pdf](http://cran.r-project.org/web/packages/party/party.pdf)で。

### データ生成
~~~~{.r}
set.seed(0)
n <- 1000
x <- rbinom(n, size=1, prob=1/3)
data.df <- data.frame(x = x,
                      tt = ifelse(x==0, rweibull(n, scale=200, shape=2), rweibull(n, scale=300, shape=3)),
                      status = rep(1,n)
                      )
~~~~

### 推定
~~~~{.r}
> library(party)
> (model <- ctree(Surv(tt, status)~x, data=data.df))

	 Conditional inference tree with 2 terminal nodes

Response:  Surv(tt, status) 
Input:  x 
Number of observations:  1000 

1) x <= 0; criterion = 1, statistic = 150.161
  2)*  weights = 664 
1) x > 0
  3)*  weights = 336 
> data.df$node <- predict(model, type="node")
~~~~

### 確認
~~~~{.r}
> fitdistr(data.df[data.df$node==2,]$tt, "weibull")
      shape          scale    
    1.98999386   202.76429299 
 (  0.06034834) (  4.16490819)
Warning message:
In densfun(x, parm[1], parm[2], ...) : NaNs produced
> fitdistr(data.df[data.df$node==3,]$tt, "weibull")
     shape        scale   
    2.974397   306.660949 
 (  0.127542) (  5.919310)
~~~~

