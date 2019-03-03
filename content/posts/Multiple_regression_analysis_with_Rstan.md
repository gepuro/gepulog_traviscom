---
title: Rstanで重回帰分析
date: 2014-12-10T23:27:52+09:00
draft: false
tags: [R言語, Rstan]
aliases:
    - /archives/108
---

ワイブルばかりだったので、メジャーな重回帰分析をしてみた。Rstanにハマりつつある。

### linear_regression.stan

~~~~{.stan}
data {
  int<lower=1> J; // number of data
  int<lower=1> K; // number of covariate
  real y[J];
  matrix[J,K] x;
}
parameters {
  real<lower=0> sd0;
  vector[K] beta;
  real beta0;
}
model {
  for(j in 1:J){
    increment_log_prob(normal_log(y[j], x[j] * beta + beta0, sd0));
  }
}
~~~~


### linear_regression.R
~~~~{.r}
library(rstan)
n <- 100
x1 <- rnorm(n, mean=1, sd=0.1)
x2 <- rnorm(n, mean=1, sd=0.1)
beta1 <- 1
beta2 <- 2
beta0 <- 3
y <- beta1 * x1 + beta2 * x2 + beta0
data.stan <- list(J = n,
                  K = 2,
                  y = y,
                  x = matrix(c(x1, x2), ncol=2))

fit <- stan(file = 'linear_regression.stan', data = data.stan, 
            iter = 1000, chains = 4)
~~~~

### 推定結果
~~~~{.text}
> fit
Inference for Stan model: linear_regression.
4 chains, each with iter=1000; warmup=500; thin=1; 
post-warmup draws per chain=500, total post-warmup draws=2000.

           mean se_mean    sd    2.5%     25%     50%     75%   97.5% n_eff  Rhat
sd0        0.00    0.00   0.0    0.00    0.00    0.00    0.00    0.00     2  5.75
beta[1]    1.00    0.00   0.0    1.00    1.00    1.00    1.00    1.00     2  6.20
beta[2]    2.00    0.00   0.0    2.00    2.00    2.00    2.00    2.00     2  4.98
beta0      3.00    0.00   0.0    3.00    3.00    3.00    3.00    3.00     2  6.53
lp__    2244.37   95.91 136.4 2073.04 2104.57 2223.56 2344.35 2440.01     2 17.83

Samples were drawn using NUTS(diag_e) at Wed Dec 10 22:45:14 2014.
For each parameter, n_eff is a crude measure of effective sample size,
and Rhat is the potential scale reduction factor on split chains (at 
convergence, Rhat=1).
~~~~



