---
title: Rubyをドットインストールで学んだ
date: 2013-10-16T11:13:55+09:00
draft: false
tags: [Ruby]
aliases:
    - /archives/66
---

普段はPythonやRを使っている人が気になった事

* print,puts,pの使い分け
* 破壊的メソッドを利用するには、"!"をつける
* 配列から複数取り出す時は、hairetu[0..2]のようにする
* hairetu[1,2]で1番目から2個分アクセスする
* hairetu[1,0]=["a","b"]で一番目の後に配列を挿入出来る
* 共通部分(&), 全体重複なし(|), 差分(-),全体重複あり(+)
* ユニーク(hairetu).uniq, ソート(hairetu.sort)
* 先頭,末尾の追加unshift,pushと削除shiftとpop
* ハッシュオブジェクト（連想配列) {"hash"=>10, "rensou"=>20}
* サイズ: rensou.size, rensou.empty?, rensou.has_key?("hash"), rensou.has_value?(10) 
* Timeクラスが便利,strftime("%Y/%m/%d")
* if then end, if, elsif,else
* =~ : 正規表現用の比較演算子 if /hoge/ =~ "fuga hoge"

参考:

* [Ruby入門 (全32回) ](http://dotinstall.com/lessons/basic_ruby)

