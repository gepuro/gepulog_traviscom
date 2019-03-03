---
title: シェルスクリプトでアクセスログを用いてページ間の類似度を求める
date: 2016-01-21T20:11:26+09:00
draft: false
tags: [データ分析]
aliases:
    - /archives/146
---

協調フィルタリングです。

協調フィルタリングに関しては、「[協調フィルタリングについてまとめてみた。](http://d.hatena.ne.jp/EulerDijkstra/20130407/1365349866)」が詳しく書かれており参考になります。

データセットは、いつものようにwebビーコンのログです。

### 使用したコマンド

* [jq](https://github.com/stedolan/jq)
* [q](http://harelba.github.io/q/)
* sed
* awk
* mcmd
* http://datascienceatthecommandline.com/
* など


### 実際のコマンド

もう少しクールに書けるような気がします。

~~~
cat data/* |  # jsonからデータを取り出してcid, urlのデータを得る
jq -c -r 'select(.site=="blog.gepuro.net")| [.cid, .url] | @csv' |
sed -e 's/\?.*"$/"/' |
grep "http://blog.gepuro.net/archives/" |
sed -e 's/http\:\/\/blog\.gepuro\.net\/archives\///' |
header -a '"cid","url"' | # cidごとにurlのビューをカウントして横持ちにする
q -H -O -d ',' "
select cid, url, count(1) as view
from -
group by cid, url
" |
mcross k=cid f=view s=url v=0 |
cat > tmp/cross.csv

cat tmp/cross.csv | # urlごとのコサイン類似度を求める
msim -d c=cosine f=$(head -n1 tmp/cross.csv | cut -c 11-) |
q -H -O -d ',' '
select fld1, fld2, cosine
from -
where fld1 != fld2
order by fld1, cosine desc

' | # urlごとに上位5件を取得する
sed -e 's/,/ /g' |
body rank ref=1 |
sed -e 's/fld1 fld2 cosine/rank fld1 fld2 cosine/' |
sed -e 's/ /,/g' |
q -H -O -d ',' '
select fld1,fld2 from -
where rank <= 5
' |
mtra k=fld1 f=fld2 | # url,類似記事一覧 というデータの持ち方をする
sed -e 's/fld1%0/fld1/' |
csvjson  # パースしやすいようにjson形式にする
~~~

### 結果

[ハードディスクの寿命分布を比較](http://blog.gepuro.net/archives/118) という記事に対しての結果を見てみます。

#### アクセスログを使った場合(メモリベース, コサイン類似度)

* [最強のハードディスクはどれだ？](http://blog.gepuro.net/archives/119)
* [dplyrの暗黒面](http://blog.gepuro.net/archives/120)
* [Rstanでワイブル回帰の推定](http://blog.gepuro.net/archives/106)

#### コンテンツベース, BoWでコサイン類似度
* [Julia 0.3で散布図を描く](http://blog.gepuro.net/archives/87)
* [Rstanでワイブル回帰の推定（説明変数複数バージョン）](http://blog.gepuro.net/archives/106)
* [Rstanでワイブル回帰の推定](http://blog.gepuro.net/archives/107)

となりました。

### さいごに

各記事の下に表示出来たら楽しいなあ。



