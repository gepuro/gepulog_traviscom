---
title: Ubuntu14.04にJulia 0.3をインストールした
date: 2014-06-15T18:01:25+09:00
draft: false
tags: [JuliaLang]
aliases:
    - /archives/86
---

[https://launchpad.net/~staticfloat/+archive/julianightlies](https://launchpad.net/~staticfloat/+archive/julianightlies)よりPPAを追加した。

~~~{.sh}
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get install julia
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています                
状態情報を読み取っています... 完了
インストールすることができないパッケージがありました。おそらく、あり得
ない状況を要求したか、(不安定版ディストリビューションを使用しているの
であれば) 必要なパッケージがまだ作成されていなかったり Incoming から移
動されていないことが考えられます。
以下の情報がこの問題を解決するために役立つかもしれません:

以下のパッケージには満たせない依存関係があります:
 julia : 依存: libcholmod1.7.1 しかし、インストールすることができません
         依存: libumfpack5.4.0 しかし、インストールすることができません
         依存: librmath-julia-dev しかし、インストールすることができません
E: 問題を解決することができません。壊れた変更禁止パッケージがあります。
~~~
と表示された。

[パッケージ: libcholmod1.7.1 (1:3.4.0-1ubuntu3) ](http://packages.ubuntu.com/ja/lucid/libcholmod1.7.1),[パッケージ: libcolamd2.7.1 (1:3.4.0-3ubuntu1) ](http://packages.ubuntu.com/ja/saucy/libcolamd2.7.1)
~~~{.sh}
$ wget http://kr.archive.ubuntu.com/ubuntu/pool/main/s/suitesparse/libcholmod1.7.1_3.4.0-1ubuntu3_amd64.deb
$ wget http://kr.archive.ubuntu.com/ubuntu/pool/main/s/suitesparse/libcolamd2.7.1_3.4.0-3ubuntu1_amd64.deb
$ wget http://kr.archive.ubuntu.com/ubuntu/pool/main/s/suitesparse/libamd2.2.0_3.4.0-1ubuntu3_amd64.deb
$ wget http://kr.archive.ubuntu.com/ubuntu/pool/main/s/suitesparse/libumfpack5.4.0_3.4.0-3ubuntu1_amd64.deb
~~~
としてから、<code>dpkg -i </code>でパッケージをインストールしたんだが、

~~~{.sh}
$ sudo apt-get install julia
パッケージリストを読み込んでいます... 完了
依存関係ツリーを作成しています                
状態情報を読み取っています... 完了
インストールすることができないパッケージがありました。おそらく、あり得
ない状況を要求したか、(不安定版ディストリビューションを使用しているの
であれば) 必要なパッケージがまだ作成されていなかったり Incoming から移
動されていないことが考えられます。
以下の情報がこの問題を解決するために役立つかもしれません:

以下のパッケージには満たせない依存関係があります:
 julia : 依存: librmath-julia-dev しかし、インストールすることができません
E: 問題を解決することができません。壊れた変更禁止パッケージがあります。
~~~
となってしまった。バイナリでインストールするのを諦めたので、ソースコードから自分でコンパイルする方法を試してみた。

過去にJuliaをインストールした時の[Juliaのインストール](http://blog.gepuro.net/archives/75)を参考に、
~~~{.sh}
$ git clone https://github.com/JuliaLang/julia.git
$ cd julia
$ make
$ sudo paco -D "cp -r usr/* /usr/local"
~~~
90分ぐらいの時間がかかった。



