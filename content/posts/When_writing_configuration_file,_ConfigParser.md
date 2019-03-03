---
title: 設定ファイルを書く時はConfigParser
date: 2013-02-16T16:27:57+09:00
draft: false
tags: [Python, product]
aliases:
    - /archives/15
---

何かプログラムを書いているときに、設定ファイルをどうするか悩むときがある。プログラムの初めの方に定数として宣言しておいて、そこを編集させるか、別のファイルに書かせるかの二通りが考えられそう。

設定項目が短い場合は、JSONで記述するのも便利そうだが、長くなってくると見難くなりそうと感じる。

今回紹介するライブラリは、[ConfigParser](http://docs.python.jp/2/library/configparser.html)です。Pythonで利用出来ます。

<pre>
import ConfigParser
CONFIG_FILE = 'settings.ini'
conf = ConfigParser.SafeConfigParser()
conf.read(CONFIG_FILE)
USERNAME = conf.get('credential', 'username')
PASSWORD = conf.get('credential', 'password')
host = conf.get('options','host')
port     = conf.get('options','port')
</pre>
Pythonコードは、上のように書くだけで良いです。

設定ファイル``settings.ini''は、
<pre>
[credential]
username=ユーザー名
password=パスワード

[options]
host=127.0.0.1
port=5000
</pre>
とこんな感じです。

割と簡単に書けるので、今後も使っていきたいですね。


