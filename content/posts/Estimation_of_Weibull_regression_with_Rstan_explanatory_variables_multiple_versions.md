---
title: Rstanでワイブル回帰の推定（説明変数複数バージョン）
date: 2014-12-04T18:43:05+09:00
draft: false
tags: [R言語, Rstan]
aliases:
    - /archives/107
---

matrixやvectorの使い方を覚えた。
### weibull_regression.stan
~~~~{.stan}
data {
  int<lower=1> J; // number of data
  int<lower=1> K; // number of covariate
  int status[J];
  real<lower=0> tt[J];
  matrix[J,K] x;
}
parameters {
  real<lower=1, upper=3> m;
  vector[K] beta;
  real beta0;
}
transformed parameters {
  real<lower=0> eta[J];
  for (j in 1:J)
    eta[j] <- exp(x[j]*beta + beta0);
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

### weibull_regression.R
~~~~{.r}
set.seed(0)
n <- 1000 # 台数
m <- 2 # 形状パラメータ
beta1 <- -1 # 係数
beta2 <- -2 # 係数
beta0 <- 10 # 切片
x1 <- rnorm(n)
x2 <- rnorm(n)
data.df <- data.frame(tt = rweibull(n, shape = m, scale=exp(beta1 * x1 + beta2 * x2 + beta0)), 
                      x1 = x1,
                      x2 = x2,
                      status=rep(1,n))
data.df[data.df$tt > 500,]$status <- 0
data.df[data.df$tt > 500,]$tt <- 500
1-sum(data.df$status)/nrow(data.df) # 打切り率 0.921

data.stan <- list(J = nrow(data.df),
                  K = 2,
                  tt = data.df$tt,
                  x = matrix(c(data.df$x1, data.df$x2), ncol=2),
                  status = data.df$status)

fit <- stan(file = 'weibull_regression.stan', data = data.stan, 
            iter = 1000, chains = 4)
~~~~

### 結果
~~~~{.text}
	mean	se_mean	sd	2.50%	25%	50%	75%	97.50%	n_eff	Rhat
m	1.92	0.01	0.18	1.58	1.79	1.92	2.04	2.26	315	1.01
beta[1]	-0.92	0	0.09	-1.12	-0.98	-0.92	-0.86	-0.76	343	1.01
beta[2]	-1.83	0.01	0.16	-2.17	-1.93	-1.82	-1.73	-1.55	342	1
beta0	9.69	0.02	0.32	9.12	9.45	9.67	9.89	10.4	306	1
eta[1]	8827.87	144.01	2637.51	5109.09	6978.65	8373.4	10139.67	15307.5	335	1.01
eta[2]	747.41	3.98	84.09	607.53	688.78	738.27	796.95	940.81	447	1.01
eta[3]	6494.17	95.31	1764.01	3938.58	5243.86	6200.7	7374.96	10705.78	343	1
eta[4]	70601.7	1978.38	35808.67	27994.1	46483.16	62399.01	84052.98	164023.29	328	1.01
eta[5]	188588.63	6537.63	114920.76	64135.67	113845.27	159425.4	226058.4	499006.32	309	1.01
・・・
~~~~

