---
title: ワイブル回帰と比例ハザードモデルのサンプルデータ生成と推定
date: 2014-12-02T17:02:18+09:00
draft: false
tags: [R言語, 統計]
aliases:
    - /archives/102
---

## ワイブル回帰
ワイブル分布を仮定して、形状パラメータ共通、尺度パラメータを説明変数で変わるモデル

\begin{align\*}
F(t|x) = 1 - \exp\left( - \left( \frac{t}{\eta(x)} \right)^m \right) \\\\
\eta(x) = \exp(\beta * x)
\end{align\*}

### ワイブル回帰のサンプルデータ
~~~~{.r}
set.seed(0)
n <- 1000 # 台数
m <- 2 # 形状パラメータ
beta <- -1 # 係数
beta0 <- 10 # 切片
x <- rnorm(n)
data.df <- data.frame(tt = rweibull(n, shape = m, scale=exp(beta * x + beta0)), x = x, status=rep(1,n))
~~~~

### weibregで推定
~~~~{.r}
> library(eha)
> weibreg(Surv(tt, status)~x, data=data.df)
Call:
weibreg(formula = Surv(tt, status) ~ x, data = data.df)

Covariate           Mean       Coef Exp(Coef)  se(Coef)    Wald p
x                  -0.927     2.017     7.513     0.059     0.000 

log(scale)                   10.016 22386.800     0.016     0.000 
log(shape)                    0.710     2.034     0.025     0.000 

Events                    1000 
Total time at risk        32057517 
Max. log. likelihood      -10619 
LR test statistic         1495 
Degrees of freedom        1 
Overall p-value           0
~~~~

### survregで推定
~~~~{.r}
> library(survival)
> survreg(Surv(tt, status) ~ x, data=data.df, dist="weibull")
Call:
survreg(formula = Surv(tt, status) ~ x, data = data.df, dist = "weibull")

Coefficients:
(Intercept)           x 
 10.0162268  -0.9916226 

Scale= 0.491728 

Loglik(model)= -10618.8   Loglik(intercept only)= -11366.3
	Chisq= 1494.89 on 1 degrees of freedom, p= 0 
n= 1000 
~~~~

## 比例ハザードモデル
ベースラインのハザード（微小時間における故障率）は共通で、ハザードが説明変数によって変動するモデル
\begin{align\*}
\lambda(t|x) = \lambda_0(t) exp(\beta*x)
\end{align\*}

### 比例ハザードモデルのサンプルデータ
~~~~{.r}
set.seed(0)
n <- 1000 # 台数
x <- rbinom(n, size=1, prob = 1/3)
tt <- runif(n , min=1, max=1000)
beta <- -1
# とりあえずベースラインはワイブル分布にしておいた
# ベースラインはノンパラなので、適当に
baseline <- pweibull(1:500, scale=100, shape=2)
hazard0 <- baseline * exp(beta * 0)
hazard1 <- baseline * exp(beta * 1)
data.df <- data.frame(tt = tt, status=ifelse(runif(tt, min=0, max=1) <= ifelse(x==0,hazard0[tt], hazard1[tt]), 1, 0), x = x)
~~~~

### coxphで推定
~~~~{.r}
> library(survival)
> coxph(Surv(tt, status) ~ x, data=data.df)
Call:
coxph(formula = Surv(tt, status) ~ x, data = data.df)


    coef exp(coef) se(coef)     z       p
x -0.979     0.376     0.15 -6.51 7.7e-11

Likelihood ratio test=51.9  on 1 df, p=5.78e-13  n= 520, number of events= 333 
   (480 observations deleted due to missingness)
~~~~

