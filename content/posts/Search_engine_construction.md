---
title: 検索エンジン構築の調べ
date: 2013-01-21T10:56:10+09:00
draft: false
tags: [Web巡回メモ]
aliases:
    - /archives/5
---

本ブログに検索窓を追加するべき調べている時のメモをまとめておく。

* [SQLiteの全文検索を使ってみる](http://blog.mwsoft.jp/article/34911345.html)
<pre>
FTS3を使って全文検索してみた。
SQLiteの全文検索というと、昔はFTS1とか2とかのモジュールを使用する方式だったみたいだけど、現在はFTS3なるものがsqlite3.exeとかに含まれているので、得に下準備をすることなく使用できるっぽい。
</pre>

* [TinySegmenterをPythonで書いてみた【Python】](http://www.programming-magic.com/20080726203844/)
<pre>
oogle App Engineではpure-Pythonなライブラリしか使えないため、Google App Engineで形態素解析をする場合には、Yahoo!の形態素解析サービスに処理を投げているものが多い。そうなると当然、他のサービスに依存してしまうため、そのサービスが落ちたりすると困ったことになる。できれば自分で形態素解析をしたいのだが、自分で全て書くには手間がかかるし、MeCabやChasenなどはpure-Pythonなものではないため使えない。 
</pre>

* [extbody -- Blog&News本文領域抽出ツール](http://tanashi.s240.xrea.com/extbody/)
<pre>
Blogのページや、ニュース記事のページから、本文を含む領域のHTMLを抽出します。
いくつもの経験則を組み合わせた抽出方法によって、80%程度の正解率で抽出できます。
日本語/英語を自動判定し、それぞれに対応した抽出を行います。
</pre>

こんなところだろうか。このあたりを組み合わせれば、簡易的な検索エンジンを構築する事は出来そう。（精度とか速度は、気にしない場合）

より良いものを実現したい場合は、Googleのカスタム検索を利用するの良いかな。本格的に、構築したい場合は、[Apache Solr](http://lucene.apache.org/solr/)を利用するのが流行りなのだろうか。

