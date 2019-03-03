---
title: 確率変数の変換
date: 2015-05-02T15:51:39+09:00
draft: false
tags: [統計]
aliases:
    - /archives/124
---

何度か勉強しているのだけれど、毎回確認しているのでブログに書いておく。



### 数式転がし

確率変数 $X$ に対して、その関数の確率分布を求める。（例: $X^2$, $\log(X)$)



変換の関数を$Y=\phi(X)$とする。$\phi(x)$は単調増加。



$\phi(x)$は単調増加であるから、$x \leq X \leq x + \Delta x$と$Y \leq Y \leq y + \Delta y$は論理的に同値であるので、


<p>
\begin{align}

P(y \leq Y \leq y + \Delta y) = P(x \leq X \leq x + \Delta x)

\end{align} 
</p>

確率変数$X$, $Y$の密度関数を$f(x)$, $g(y)$とおく。



ここで、$P(x \leq X \leq x + \Delta x) = f(x) \Delta x$であるから、

<p>
\begin{align}

g(y) = f(x) \Delta x / \Delta y \approx f(x) ( dx / dy )

\end{align}
</p>

となる。



関数$y=\phi(x)$をxについて解いた逆関数を$x=\psi(y)$とすると、


<p>
\begin{align}

g(y) = f(\psi(y)) * | d \psi(y) / d y |

\end{align}
</p>


として求められる。



### 例

確率変数$X \sim uni(0, 1)$に対して、

$y = x^2$（ただし$0 \leq x$)と変換する時の密度関数を求める。



$y = \phi(x) = x^2$の逆関数は、$x = \psi(y) = \sqrt{y}$となる。



よって、


<p>
\begin{align}

g(y) &= f(\psi(y)) * | d \psi(y) / d y | \\\\

& = 1 * d / dy \sqrt{y} \\\\

& = \frac{1}{2 \sqrt{y}}

\end{align}
</p>


となる。





### 参考

 

 * [統計学入門 (基礎統計学)](http://www.amazon.co.jp/%E7%B5%B1%E8%A8%88%E5%AD%A6%E5%85%A5%E9%96%80-%E5%9F%BA%E7%A4%8E%E7%B5%B1%E8%A8%88%E5%AD%A6-%E6%9D%B1%E4%BA%AC%E5%A4%A7%E5%AD%A6%E6%95%99%E9%A4%8A%E5%AD%A6%E9%83%A8%E7%B5%B1%E8%A8%88%E5%AD%A6%E6%95%99%E5%AE%A4/dp/4130420658)

