---
title: jqコマンドで正規表現を使う
date: 2016-01-24T17:29:34+09:00
draft: false
tags: [jq, シェルスクリプト]
aliases:
    - /archives/150
---

日常的に利用しているjqコマンドですが、最新バージョンを入れれば正規表現が使えると知ったので、インストールしました。
OSはubuntu 14.04です。

基本的には、
[https://stedolan.github.io/jq/download/](https://stedolan.github.io/jq/download/)に従えばよいですが、正規表現を利用するには外部ライブラリのインストールが必要なようです。

下のようにしてインストールした。余分なライブラリが入っているだろうが、調査するのが面倒だったので、<code>apt-cache search onig</code>でヒットしたものを全部突っ込んだ。
~~~
sudo apt-get install libjruby-joni-java libonig-dev libonig2 libonig2-dbg lua-rex-onig lua-rex-onig-dev
~~~

あとは、公式の通りにインストールするだけ。
~~~
git clone https://github.com/stedolan/jq.git
cd jq
autoreconf -i
./configure --disable-maintainer-mode
make
sudo make install
~~~

手元の環境では、<code>./configure --disable-maintainer-mode --prefix=/usr/local/</code>をしておいたり、<code>paco</code>を使ってインストールしています。



