---
title: 集計時の順序を曜日で並べる
date: 2014-12-22T18:31:01+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/113
---

weekdays()を使って、Date型から曜日を取得できる。
~~~{.r}
> (today <- Sys.Date())
[1] "2014-12-22"
> str(weekdays(today))
 chr "月曜日"
~~~

戻り値が文字列になってるので、集計時に曜日順にならない。
~~~{.r}
> x <- c("月曜日","日曜日", "火曜日", "日曜日", "土曜日")
> table(x)
x
火曜日 月曜日 土曜日 日曜日 
     1      1      1      2 
~~~


factor型にしてlevelsを指定することによって、順番を制御できる。
~~~{.r}
> yobi <- factor(x, levels=paste(c("日", "月", "火", "水", "木", "金", "土"), "曜日", sep=""))
> table(yobi)
yobi
日曜日 月曜日 火曜日 水曜日 木曜日 金曜日 土曜日 
     2      1      1      0      0      0      1 
~~~

嬉しい！

twitterで教えてもらった書き方は、[曜日順での集計に関するメモ書き](http://rpubs.com/yamano357/52183) です。

