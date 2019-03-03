---
title: dplyrのWindowed rank functionsを試してみる
date: 2014-11-05T00:08:56+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/100
---

何気なく下の動画を眺めてたら、window functionが紹介されてたので、手元で試してみる

<iframe width="560" height="315" src="//www.youtube.com/embed/Ue08LVuk790" frameborder="0" allowfullscreen></iframe>

helpに載っているサンプルコードの結果
~~~~{.r}
> x <- c(5, 1, 3, 2, 2)
> row_number(x)
[1] 5 1 4 2 3
> min_rank(x)
[1] 5 1 4 2 2
> dense_rank(x)
[1] 4 1 3 2 2
> percent_rank(x)
[1] 1.00 0.00 0.75 0.25 0.25
> cum_dist(x)
[1] 1.0 0.2 0.8 0.6 0.6
> ntile(x, 2)
[1] 2 1 2 1 1
~~~~


 * row_number : ランクを求める。（同点は先に出た方が初めになる）
 * min_rank : ランクを求める。（同点は同じランクになる）
 * dense_rank : min_rankの逆
 * percent_rank : min_rankを[0, 1]でリスケールしたもの
 * cum_dist : persent_rankの累積和
 * ntile : ランクを引数の中でつける。

詳細は
~~~~{.r}
help(ranking)
~~~~
でどうぞ。

関数の内部ではrank()を使ってるみたいだ。。。
~~~~{.r}
> dplyr::min_rank
function (x) 
rank(x, ties.method = "min")
<environment: namespace:dplyr>
~~~~

