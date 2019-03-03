---
title: サイトのロゴ画像はクリックされるのか
date: 2016-01-08T01:39:01+09:00
draft: false
tags: [データ分析]
aliases:
    - /archives/144
---

一般的なwebサイトでは、タイトルのロゴ画像にトップページへのリンクを張っています。とは言え、これをクリックしてくれる人って、一体何人いるのだろうかと疑問に感じたので、調べてみました。

### 計測方法

ロゴ画像のリンクを「http://blog.gepuro.net/?from=logo」として、webビーコンを確認する。

JSONで保存しているので、jqコマンドやqコマンドを下のように使う。
~~~{.sh}
cat data/* |
jq -r '
select(.site=="blog.gepuro.net") |
select(.unixtime >= 1450334421 and .unixtime <= 1452177993) |
[.sid, .url] | @csv' |
header -a '"sid","url"' |
q -H -O -d ',' '
select sid,
max(
case 
when url = "http://blog.gepuro.net/?from=logo" then 1 else 0
end
) as logo_flag
from -
group by sid
' |
q -H -O -d ',' '
select
count(1) as session_cnt,
sum(logo_flag) as click_logo_cnt,
1.0 * sum(logo_flag) / count(1) as click_logo_rate
from -
'

~~~

結果は、

|session数|ロゴのクリック人数|ロゴのクリック率|
|-:|-:|^:|
|635|10|0.016|

となった。

ほぼ誰もクリックしないのかよ・・・。orz


### twitter上の反応
<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">サイトのロゴ画像はほんとに左上のすみにある場合はクリックする場合が多い</p>&mdash; 自称エンジニア (@neotaso_) <a href="https://twitter.com/neotaso_/status/685140248280469505">January 7, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">サイトのロゴは「サイトの誘導がクソなので行きたい場所が見つからないからとりあえずホームページに戻る」ときの道標</p>&mdash; 大須ういろ (@hakojima) <a href="https://twitter.com/hakojima/status/685140741232832512">January 7, 2016</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

