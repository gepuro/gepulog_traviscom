---
title: トップページからページングして2ページ目へのアクセスはあるのか
date: 2016-01-18T23:36:06+09:00
draft: false
tags: [データ分析]
aliases:
    - /archives/145
---

サイトの顔になるトップページですが、過去の記事へと遡ってくれる人は、どれほどいるのでしょうか。

蓄積しているログを集計しました。
集計期間は、2015/9/14から2016/1/18です。

トップページのPV数482のうち、2ページ目への遷移は16でした。
また、遷移した合計数は123でした。

意外にもサイト内を巡回してくれるのですね。


遷移先を上位から

|URL|PV数|
|-|-|
|[http://blog.gepuro.net/page/2](http://blog.gepuro.net/page/2) | 16 |
|[http://blog.gepuro.net/archives/118](http://blog.gepuro.net/archives/118) | 9 |
|[http://blog.gepuro.net/category/統計/](http://blog.gepuro.net/category/統計/ ) | 8 |
|[http://blog.gepuro.net/archives/131](http://blog.gepuro.net/archives/131) | 7 |
|[http://blog.gepuro.net/archives/129](http://blog.gepuro.net/archives/129) | 6 |
|[http://blog.gepuro.net/archives/133](http://blog.gepuro.net/archives/133) | 6 |
|[http://blog.gepuro.net/archives/136](http://blog.gepuro.net/archives/136) | 6 |
|[http://blog.gepuro.net/archives/134](http://blog.gepuro.net/archives/134) | 5 |
|[http://blog.gepuro.net/archives/135](http://blog.gepuro.net/archives/135) | 5 |
|[http://blog.gepuro.net/archives/119](http://blog.gepuro.net/archives/119) | 4 |

となっていました。

集計期間から推測すると、右側にある「最近の投稿一覧」もクリックされているようです。


他の記事へアクセスして貰いやすくするためには、自動ロードも必要なのかもしれないです。
[http://www.infinite-scroll.com/infinite-scroll-jquery-plugin/](http://www.infinite-scroll.com/infinite-scroll-jquery-plugin/)　こういうのも使ってみたいなあ。


### 集計用のコマンド
~~~
cat data/* |
jq -r '
select(.url == "http://blog.gepuro.net/") |
[.sid, .date, .url] | @csv' |
q -H -O -d ',' '
select
count(1) as pv_toppage
from -
'

echo

cat data/* |
jq -r '
select(.site=="blog.gepuro.net") |
select(.referrer == "http://blog.gepuro.net/") |
select(.url != "http://blog.gepuro.net/") |
[.sid, .url] | @csv' |
awk 'BEGIN{FS=","}{if($2~/http:\/\/blog\.gepuro\.net\/page/){cnt+=1}}END{print NR","cnt","cnt/NR}' |
header -a '"トップページからの遷移数","ページング数", "割合"'

echo

cat data/* |
jq -r '
select(.site=="blog.gepuro.net") |
select(.referrer == "http://blog.gepuro.net/") |
select(.url != "http://blog.gepuro.net/") |
[.sid, .url] | @csv' |
header -a '"sid","url"' |
q -H -O -d ',' '
select
url,
count(1) as pv_url
from -
group by url
order by 2 desc
limit 10
'
~~~

jqコマンド便利だなあ。


