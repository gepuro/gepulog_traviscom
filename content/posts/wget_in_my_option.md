---
title: wgetで便利なオプション達
date: 2019-12-19T23:00:00+09:00
draft: false
tags: [クローラー]
---

wgetのオプションは非常に豊富にあって調べきれないが、自身が便利に感じた機能をまとめました。

### --recursive
サイトを再帰的にクローリングする

### -e robots=off
robots.txtに従わない。
行儀の良いクローラーを作る時には使ってはならない。

### --level=3
3階層目までクローリング対象にする。
`--level=inf`にすれば無限になる。大きなサイトを対象にすると長い時間終わらなくなるので、ある程度絞ること。

## --random-wait
クローリングの待ち時間をランダムにする。`-w`もしくは`--wait`で指定して待ち時間を0.5倍から1.5倍にする。

### --wait=1
クローリング間隔を1秒にする

### --convert-links
ダウンロードしたファイルが外部参照できるようにリンクを書き換える

### --no-check-certificate
サーバ証明書の問い合わせを無効にする

### --no-parent
指定したURLより親階層をクローリング対象にしない。これは必須

### --adjust-extension
拡張子がついてないファイルに`.html`などの拡張子をつける。保存したファイルを読みやすくなる。

### --restrict-file-names=nocontrol,windows,ascii
パーセントエンコードした形のファイル名にする。日本語がURLに含まれている時の対策

### --reject-regex='hoge|fuga|'
正規表現でクロールの対象外にするURLを指定する

### --user-agent="Mozilla/10.0 (Windows NT 10.0) AppleWebKit/538.36 (KHTML, like Gecko) Chrome/69.420 Safari/537.36"
ユーザーエージェントの偽装

### -nv
`--no-verbose`とも。出力結果を簡易表示する

### --timeout=10
10秒でタイムアウトする。指定しないと永遠に問い合わせを待ってしまうことがある。

### --secure-protocol=auto
SSLv2やSSLv3などのプロトコルを自動的に選択する
