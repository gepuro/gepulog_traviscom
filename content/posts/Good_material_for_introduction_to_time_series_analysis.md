---
title: 時系列解析の入門に良い資料
date: 2015-04-29T10:00:36+09:00
draft: false
tags: [統計, 時系列]
aliases:
    - /archives/123
---

オハイオ州立大学のRobert de Jong先生が公開されている講義資料？がよさ気なので、紹介します。

* [Lecture 1: Stationary Time Series∗](http://www.econ.ohio-state.edu/dejong/note1.pdf)
 * [Lecture 2: ARMA Models∗](http://www.econ.ohio-state.edu/dejong/note2.pdf)
 * [Lecture 3: Spectral Analysis∗](http://www.econ.ohio-state.edu/dejong/note3.pdf)
 * [Lecture 4: Asymptotic Distribution Theory∗](http://www.econ.ohio-state.edu/dejong/note4.pdf)
 * [Lecture 5: Linear Regressions∗](http://www.econ.ohio-state.edu/dejong/note5.pdf)
 * [Lecture 6: Vector Autoregression∗](http://www.econ.ohio-state.edu/dejong/note6.pdf)
 * [Lecture 7: Processes with Deterministic Trends∗](http://www.econ.ohio-state.edu/dejong/note7.pdf)
 * [Lecture 8: Univariate Processes with Unit Roots∗](http://www.econ.ohio-state.edu/dejong/note8.pdf)
 * [Lecture 9: Multivariate Unit Root Processes and
Cointegration](http://www.econ.ohio-state.edu/dejong/note9.pdf)
 * [Lecture 10: Further Topics∗](http://www.econ.ohio-state.edu/dejong/note10.pdf)

### 誤植
部分的にしか読んでいないけど、誤植がありました。他にも誤植がありそう。
### Lecture 7
#### 式(3)
\begin{align\*}
\hat{\beta_n} = \left[\sum_{t=1}^n x_t x_t ' \right]^{-1} \left[ \sum_{t=1}^n x_t ' y_t \right]
\end{align\*}

だと思われます。（参考：自然科学の統計学）式(4)も転置記号が抜けてますね。

#### Proposition 1の1つ前の式
\begin{align\*}
Q^{-1} H_n^{-1}\left(\sum_{t=1}^n x_t u_t \right ) = Q^{-1} \begin{bmatrix}
n^{-1/2} \sum_{t=1}^n u_t \\\\ 
n^{-3/2} \sum_{t=1}^n (t/n) u_t
\end{bmatrix}
\end{align\*}

かな。そうじゃないと、辻褄が合わないかと。

