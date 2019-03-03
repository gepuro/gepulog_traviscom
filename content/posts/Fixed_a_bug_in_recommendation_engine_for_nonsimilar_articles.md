---
title: 似てない記事の推薦エンジンのバグを直した
date: 2013-10-14T14:52:55+09:00
draft: false
tags: [Python, product, データマイニング]
aliases:
    - /archives/65
---

先日より、各記事の下部に似てない記事を表示させていたが、記事によって表示されないものがあった。原因が分かったので、修正を行いました。

cos類似度を計算する時に、各記事の組み合わせを作成する箇所でバグがありました。
本来は、
<pre>
itertools.permutations
</pre>
とするところを、
<pre>
itertools.combinations
</pre>
としていました。

現段階では、A,Bの類似度とB,Aの類似度を二度計算しているので、記事の数が増えてくると修正を加える必要が出てきそうです。

参考:

- [10.1. itertools — 効率的なループ実行のためのイテレータ生成関数](http://docs.python.jp/3/library/itertools.html)

