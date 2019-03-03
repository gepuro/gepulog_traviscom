---
title: Rstanでワイブル分布のパラメータ推定（打切りのあるデータで）
date: 2014-12-04T03:35:00+09:00
draft: false
tags: [R言語, Rstan]
aliases:
    - /archives/105
---

### weibull.stan
~~~~{.stan}
data {
  int<lower=1> J; // number of data
  int status[J];
  real<lower=0> tt[J];
}
parameters {
  real<lower=1> m; 
  real<lower=1> eta;
}
model {
  for(j in 1:J){
    if(status[j] == 1){
      increment_log_prob(weibull_log(tt[j], m, eta));
    }
    if(status[j] == 0){
      increment_log_prob(weibull_ccdf_log(tt[j], m, eta));
    }
  }
}
~~~~

### weibull.R
#### サンプルデータの生成
~~~~{.R}
> set.seed(0)
> data.df <- data.frame(tt = rweibull(1000, shape = 2, scale = 100),
+                       status = rep(1,1000))
> data.df[data.df$tt > 30,]$status <- 0
> data.df[data.df$tt > 30,]$tt <- 30
> 1-sum(data.df$status)/nrow(data.df) # 打切り率
[1] 0.904
~~~~

#### Rstan用データと推定
~~~~{.R}
data.stan <- list(J = nrow(data.df),
                  tt = data.df$tt,
                  status = data.df$status)

fit <- stan(file = 'weibull.stan', data = data.stan, 
            iter = 1000, chains = 4)
~~~~

#### 推定結果
~~~~{.R}
> fit
Inference for Stan model: weibull.
4 chains, each with iter=1000; warmup=500; thin=1; 
post-warmup draws per chain=500, total post-warmup draws=2000.

        mean se_mean    sd    2.5%     25%     50%     75%   97.5% n_eff Rhat
m       1.96    0.01  0.19    1.61    1.82    1.94    2.09    2.37   349 1.01
eta    99.49    0.70 13.03   77.71   89.63   98.45  108.25  128.27   351 1.01
lp__ -622.54    0.04  0.96 -625.07 -622.94 -622.28 -621.84 -621.53   566 1.00

Samples were drawn using NUTS(diag_e) at Thu Dec 04 03:30:48 2014.
For each parameter, n_eff is a crude measure of effective sample size,
and Rhat is the potential scale reduction factor on split chains (at 
convergence, Rhat=1).
~~~~

