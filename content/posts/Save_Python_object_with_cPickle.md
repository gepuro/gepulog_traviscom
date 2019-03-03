---
title: cPickleでPythonオブジェクトを保存する
date: 2013-02-12T17:30:30+09:00
draft: false
tags: [Python, product]
aliases:
    - /archives/13
---

Pythonでは、[cPickle](http://docs.python.jp/2.4/lib/module-cPickle.html)(または[pickle](http://docs.python.jp/2.4/lib/module-pickle.html))、[shelve](http://docs.python.jp/2/library/shelve.html)というように変数を永続化することが出来るモジュールがあります。

用途としては、データベースを使用するほどの規模ではないが、データを保存しておきたい時に便利です。

shelveを使えば、辞書型を保存しておくことができます。
今回、紹介するcPickleは、Pythonオブジェクトをシリアライズして保存することができます。例えば、辞書型、配列型、文字列といった様々なデータを保存しておくことが出来ます。

cPickleとPickleの大きな違いは、実行速度です。cPickleの方が最大で1000倍早いそう。(自身では未確認)

このgepulogでは、cPickleを利用しています。使っている場所は、サイドバーにあるカテゴリと最近の更新の部分です。こちらのデータは、変化が少なく、同じデータを何度も表示するので、データベースへ毎回アクセスするよりも早くなるはず。([サイトのレスポンスを表示してくれるサイト](http://site-speed.podzone.net/)では早くなっているように見える)

編集者用の画面をもう少し直したいなあ。

