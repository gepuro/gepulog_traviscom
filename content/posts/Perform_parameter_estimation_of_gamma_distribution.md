---
title: ガンマ分布のパラメータ推定をする
date: 2014-07-22T18:54:59+09:00
draft: false
tags: [R言語, 統計]
aliases:
    - /archives/93
---

R言語でガンマ分布のパラメータを推定する方法を2通り示す。

####  データの生成
~~~{code}
> x <- rgamma(1000, shape=2, rate=0.5)
~~~

#### 最尤法による推定
~~~{code}
> fitdistr(x, "gamma")
     shape         rate   
  1.81070179   0.45850801 
 (0.07471446) (0.02177244)
~~~

#### モーメント法による推定
~~~~{code}
> med.gam <- mean(x)
> var.gam <- var(x)
> (shape <- med.gam/var.gam)
[1] 0.45973
> (rate <- ((med.gam)^2)/var.gam)
[1] 1.815528
~~~~

参考

* [http://cran.r-project.org/doc/contrib/Ricci-distributions-en.pdf](http://cran.r-project.org/doc/contrib/Ricci-distributions-en.pdf)
* [http://lokad.jp/actuary/wp-content/uploads/2010/09/Gamma.pdf](http://lokad.jp/actuary/wp-content/uploads/2010/09/Gamma.pdf)
* [http://ja.wikipedia.org/wiki/%E3%82%AC%E3%83%B3%E3%83%9E%E5%88%86%E5%B8%83](http://ja.wikipedia.org/wiki/%E3%82%AC%E3%83%B3%E3%83%9E%E5%88%86%E5%B8%83)

