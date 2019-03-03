---
title: Rstanでワイブル分布のパラメータ推定
date: 2014-12-03T23:25:57+09:00
draft: false
tags: [R言語, Rstan]
aliases:
    - /archives/104
---

Rstanでワイブル分布のパラメータ推定をしてみる。打切りを考慮するにはどうしたら良いのだろうか？
[Stan Modeling Language User's Guide and Reference Manual,  v2.5.0](https://github.com/stan-dev/cmdstan/releases/download/v2.5.0/stan-reference-2.5.0.pdf)の83ページ辺りを読めば出来そう。

### weibull.stan
~~~~{.stan}
data {
  int<lower=0> J; // number of data 
  real<lower=0> tt[J]; // time
}
parameters {
  real<lower=0> m; 
  real<lower=0> eta;
}
model {
  for(j in 1:J)
    tt[j] ~ weibull(m, eta);
}
~~~~

### weibull.R
~~~~{.r}
library(rstan)
data.ls <- list(J = 1000,
                tt = rweibull(1000, shape = 2, scale = 100))

fit <- stan(file = 'weibull.stan', data = data.ls, 
            iter = 1000, chains = 4)
~~~~

### 結果
~~~~{.text}
> fit
Inference for Stan model: weibull.
4 chains, each with iter=1000; warmup=500; thin=1; 
post-warmup draws per chain=500, total post-warmup draws=2000.

         mean se_mean   sd     2.5%      25%      50%      75%    97.5% n_eff Rhat
m        2.02    0.00 0.05     1.93     1.99     2.02     2.05     2.12   946    1
eta    101.42    0.05 1.67    98.32   100.24   101.39   102.60   104.82  1007    1
lp__ -5197.22    0.04 0.97 -5199.81 -5197.58 -5196.91 -5196.55 -5196.28   735    1

Samples were drawn using NUTS(diag_e) at Wed Dec 03 23:23:05 2014.
For each parameter, n_eff is a crude measure of effective sample size,
and Rhat is the potential scale reduction factor on split chains (at 
convergence, Rhat=1).
~~~~

