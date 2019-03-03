---
title: Rstanでワイブル回帰の推定
date: 2014-12-04T12:17:47+09:00
draft: false
tags: [R言語, Rstan]
aliases:
    - /archives/106
---

### weibull_regression.stan
~~~~{.stan}
data {
  int<lower=1> J; // number of data
  int status[J];
  real<lower=0> tt[J];
  real x[J];
}
parameters {
  real<lower=1, upper=3> m;
  real beta;
  real beta0;
}
transformed parameters {
  real<lower=0> eta[J];
  for (j in 1:J)
    eta[j] <- exp(beta * x[j] + beta0);
}
model {
  for(j in 1:J){
    if(status[j] == 1){
      increment_log_prob(weibull_log(tt[j], m, eta[j]));
    }
    if(status[j] == 0){
      increment_log_prob(weibull_ccdf_log(tt[j], m, eta[j]));
    }
  }
}
~~~~
mを1から3ぐらいにしておかないと、推定値が遥か彼方に飛ばされることがある。ワイブル分布は厄介やね。

### weibull_regression.R
~~~~{.r}
library(rstan)

set.seed(0)
n <- 1000 # 台数
m <- 2 # 形状パラメータ
beta <- -1 # 係数
beta0 <- 10 # 切片
x <- rnorm(n)
data.df <- data.frame(tt = rweibull(n, shape = m, scale=exp(beta * x + beta0)), 
                      x = x, 
                      status=rep(1,n))
data.df[data.df$tt > 3000,]$status <- 0
data.df[data.df$tt > 3000,]$tt <- 3000
1-sum(data.df$status)/nrow(data.df) # 打切り率 0.929

data.stan <- list(J = nrow(data.df),
                  tt = data.df$tt,
                  x = x,
                  status = data.df$status)

fit <- stan(file = 'weibull_regression.stan', data = data.stan, 
            iter = 1000, chains = 4)

~~~~

### 結果
~~~~{.text}
	mean	se_mean	sd	2.50%	25%	50%	75%	97.50%	n_eff	Rhat
m	1.94	0.01	0.22	1.53	1.8	1.93	2.08	2.39	311	1.01
beta	-1.03	0.01	0.13	-1.3	-1.11	-1.02	-0.94	-0.8	303	1.01
beta0	10.13	0.02	0.27	9.64	9.94	10.1	10.3	10.7	292	1.01
eta[1]	6901.33	47.51	887.55	5532.96	6292.8	6791.62	7381.7	8862.99	349	1.01
eta[2]	36854.06	763.24	12739.71	19890.77	28340.35	33948.03	42868.47	67365.57	279	1.01
eta[3]	6438.14	41.13	784.76	5214.99	5898.46	6343.24	6861.3	8225.53	364	1.01
eta[4]	6833.67	46.56	872.2	5486.28	6236.14	6725.15	7303.96	8769.99	351	1.01
eta[5]	16780.77	231.8	3926.93	11085.77	14076.74	16048.53	18780.13	26145.32	287	1.01
・・・・
~~~~



