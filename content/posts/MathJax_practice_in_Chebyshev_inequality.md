---
title: チェビシェフの不等式でMathJaxの練習
date: 2013-08-07T23:59:38+09:00
draft: false
tags: [統計]
aliases:
    - /archives/46
---

MathJax使いやすいですね。

\begin{align\*}
 \sigma^2 & = \int_{\infty}^{\infty} (x-\mu)^2 f(x) dx \\\\
   & = \int_{\infty}^{\mu - k\sigma} (x-\mu)^2 f(x) dx + \int_{\mu-k\sigma}^{\mu+k\sigma} (x-\mu) ^2 f(x) dx + \int_{\mu+k\sigma}^{\infty} (x-\mu)^2 f(x) dx \\\\
   & \geq \int_{-infty}^{\mu-k\sigma} (x-\mu)^2 f(x) dx + \int_{\mu+k\sigma}^{\infty} (x-\mu)^2 f(x) dx \\\\
& = \int_{|x-\mu| \geq k \sigma} (x-\mu)^2 f(x) dx \\\\
& \geq \int_{|x-\mu| \geq k\sigma} (x-\mu)^2 f(x) dx \\\\
& \geq k^2 \sigma^2 \int_{|x-\mu| \geq k \sigma} f(x) dx \\\\
& = k^2 \sigma^2 P(|X-\mu| \geq k \sigma)
\end{align\*}
よって、
\begin{align\*}
P(|X - \mu| \geq k \sigma) \leq \frac{1}{k^2} 
\end{align\*}
となる。

参考：
[チェビシェフの定理](http://homepage3.nifty.com/rikei-index01/kakuritu/chebisyehu.html)



