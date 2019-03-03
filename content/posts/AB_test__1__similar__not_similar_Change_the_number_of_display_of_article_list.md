---
title: ABテスト#1 - (似ている|似てない)記事一覧の表示数を変える
date: 2016-01-07T00:53:51+09:00
draft: false
tags: [web, プロダクト, ABテスト]
aliases:
    - /archives/143
---

[ABテストを実装&実施してみる ](http://blog.gepuro.net/archives/141)で試してみたABテストの結果です。

個別の記事を開いた時、下部に表示させていた"似ている記事"、"似ていない記事"の表示数を変えてみました。

* パターンA: それぞれ5件
* パターンB: それぞれ3件


結果は、

|パターン|pv数|session数|pv数/session数|
|-|-|-|-|
| A | 333 | 230 | 1.45 |
| B | 297 | 226 | 1.31 |


[ウィルコクソンの順位和検定](http://bio-info.biz/statistics/test_wilcoxon_rank_sum.html)を使いました。

~~~
Asymptotic Wilcoxon rank sum test
data:  df[df$ab == 0, ]$pv and df[df$ab == 1, ]$pv
W = 25835, p-value = 0.02378
alternative hypothesis: true mu is not equal to 0
~~~

パターンAとパターンBのpv数は、有意水準5%で中央値が等しいという帰無仮説を棄却する。


パターンAの勝ちですね。


