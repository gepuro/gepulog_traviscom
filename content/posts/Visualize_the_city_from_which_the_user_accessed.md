---
title: ユーザのアクセス元の街を可視化してみる
date: 2016-01-22T21:39:06+09:00
draft: false
tags: [データ分析]
aliases:
    - /archives/147
---

当ブログのユーザのアクセス元を可視化しました。

アクセス元のipアドレスから[https://freegeoip.net/](https://freegeoip.net/)より提供されるAPIを用いて、どの街かを特定しました。

観測期間は適当です。(freegeoipでデータを取得し始めてからで、過去に遡るのは面倒だった)

以下が結果です。パレート図で描きました。

<img src="https://www.dropbox.com/s/m0bphve2h8b26c2/count_city.png?dl=1">

久しぶりにパレート図を見たわけですが、円グラフに比べて見やすいなあと感じます。もっと流行るとイイなあ。

それにしても、mountain viewって何だろうか。

最後にコードです。

~~~
cat data/* |
jq -r '
select(.site=="blog.gepuro.net") |
select(.geo.city != null) |
[.cid, .geo.city] |
@csv' |
sort |
uniq |
header -a '"cid","city"' |
q -H -O -d ',' '
select
case city
when "" then "unknown"
else city
end as city
, count(1)  as count
from -
group by city
order by 2 desc
' |
Rio -r -e 'df %>% mutate(crank=1:n())' |
Rio -r -e 'rbind(df[df$crank<10,c("city","count")], data.frame(city="other", count=sum(df[df$crank > 10,]$count)))' |
Rio -e 'library(qcc); library("devtools"); source("https://gist.githubusercontent.com/abikoushi/722ce195fb70df5cc679/raw/fd434a74d86ab3feb415da33b3f8abb4dd03355f/ParetoChart2.r"); city <- df$count; names(city) <- df$city; png("count_city.png"); pareto.chart2(city, unsorted=TRUE); dev.off()'
~~~

Rioを使ったら、読みにくくなってしまった。
qコマンドやjqコマンドと違って、改行を許してくれないんだよなあ。。。
(自分で直せって話だな）


