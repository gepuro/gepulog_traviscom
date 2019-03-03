---
title: library(ranger)とlibrary(randomForest)の速度を比較する
date: 2015-09-10T23:06:48+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/130
---

<p><a href="http://www.r-bloggers.com/predicting-titanic-deaths-on-kaggle-v-ranger/">Predicting Titanic deaths on Kaggle V: Ranger</a>を読んでいたら、 「ranger早いぜ」というのを見つけたので試してみた。</p>
<pre class="r"><code>library(&quot;ranger&quot;)
library(&quot;randomForest&quot;)</code></pre>
<pre><code>## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
## 
## Attaching package: 'randomForest'
## 
## The following object is masked from 'package:ranger':
## 
##     importance</code></pre>
<pre class="r"><code>data(&quot;iris&quot;)

# http://d.hatena.ne.jp/a_bicky/20101225/1293258748
test &lt;- function(code, ic = 1000) {
    expr &lt;- substitute(code)
    print(system.time(for (i in 1:ic) {
        eval(expr)
    }))   
}
test(
  ranger(Sepal.Length~., data=iris, mtry=2, num.trees = 500,
         write.forest=TRUE)
)</code></pre>
<pre><code>##    user  system elapsed 
##  71.426  26.430  53.740</code></pre>
<pre class="r"><code>test(
  randomForest(Sepal.Length~., data=iris, mtry=2, ntree = 500)
)</code></pre>
<pre><code>##    user  system elapsed 
## 136.479   0.000 140.745</code></pre>
<p>確かに早いですね。</p>


