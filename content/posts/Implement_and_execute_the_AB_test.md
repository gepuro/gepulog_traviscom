---
title: ABテストを実装&実施してみる
date: 2015-12-23T16:19:07+09:00
draft: false
tags: [web, プロダクト]
aliases:
    - /archives/141
---

このブログでABテストを実施するにあたって、悩んだ事をつらつらと書きます。

## 既にあるwebビーコンを活かしたい

[webビーコンを作った ](http://blog.gepuro.net/archives/131)にあるように、このブログには自作ビーコンが埋め込まれています。google analyticsでは拾いきれないログを取得する動機で作ったのですが、これを活かしきれていませんでした。

今回はABテストを実装する上で、このログを有効活用しようと思います。


## 悩んだ実装方法

* アプリ側でwebビーコンが持つIDを使いたい
    * 新規ユーザの場合、htmlのレンダリングが先なので、IDを特定できない
 * webビーコンにABテストを仕込む
    * アプリとwebビーコンが密結合になってしまう
    * 独立したサービスとして動かしたい
* ABテスト用のアプリを新たにつくる
    * dom操作で書き換えれば出来そうだが、重くなりそう

## 最終的な実装方法

* アプリ側でアクセス元のipアドレスを取得
* 偶数ならA、奇数ならBを出す

## 検証方法

webビーコンにABというカラムを追加して、偶数か奇数かを記録しておけば、後から確認が簡単になる。

ワンライナーで、こんな感じで現状を確認出来るようにした。
~~~
cat data/* |
jq -r '
select(.site=="blog.gepuro.net") |
select(.ab==1 or .ab==0) |
select(.ymd >= 20151221) |
[.sid, .ab] | @csv' |
header -a '"sid","ab"' |
csvsql --query "select sid,ab,count(1) as view from stdin group by sid, ab" |
csvsql --query "
select
ab,
1.0*sum(view)/count(1) as pps,
sum(view) as view_cnt,
count(1) as session_cnt
from stdin
group by ab"
~~~

慣れているという理由で[http://datascienceatthecommandline.com/](http://datascienceatthecommandline.com/)に紹介されているcsvsqlを利用したけど、最近の流行りは[q - Text as Data](http://harelba.github.io/q/index.html)だと思うので、試したいな。

ABテストの内容は、後日報告します！

