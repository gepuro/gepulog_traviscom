---
title: '「npm: 依存: node-gyp (>= 0.10.9) しかし、インストールされようとしていません」の解決'
date: 2020-01-17T20:30:00+09:00
draft: false
tags: [nodejs]
---

久しぶりにnodejs周りを弄っていたら次のエラーがでて、npmをインストールできませんでした。自分はUbuntu 18.04を使っています。

```
gepuro$ sudo apt-get install -y npm
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています                
状態情報を読み取っています... 完了
インストールすることができないパッケージがありました。おそらく、あり得
ない状況を要求したか、(不安定版ディストリビューションを使用しているの
であれば) 必要なパッケージがまだ作成されていなかったり Incoming から移
動されていないことが考えられます。
以下の情報がこの問題を解決するために役立つかもしれません:

以下のパッケージには満たせない依存関係があります:
 npm : 依存: node-gyp (>= 0.10.9) しかし、インストールされようとしていません
E: 問題を解決することができません。壊れた変更禁止パッケージがあります。
```

解決策は、[npm : Depends: node-gyp (>= 0.10.9) but it is not going to be installed [duplicate]](https://askubuntu.com/questions/1088662/npm-depends-node-gyp-0-10-9-but-it-is-not-going-to-be-installed)に書かれていました。

```
gepuro$ sudo apt-get install nodejs-dev node-gyp libssl1.0-dev
```

をしてから、

```
gepuro$ sudo apt-get install npm
```

です。

パッケージの依存関係で手こずるのはUbuntuでは久しぶりですね。

