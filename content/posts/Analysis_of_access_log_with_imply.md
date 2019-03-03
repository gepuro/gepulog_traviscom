---
title: implyでアクセスログの分析
date: 2015-10-29T10:28:59+09:00
draft: false
tags: [druid, pivot, imply]
aliases:
    - /archives/136
---

[webビーコンを作った ](http://blog.gepuro.net/archives/131)という記事を投稿してから、暫く経ちました。

データも集まり初めてきたところで、分析を始めたいと思います。

最近、僕の中で話題の[druid](http://druid.io)という時系列なデータが得意なデータベースがあります。
このデータベースは、秒間300万イベントでも捌けるらしいです。（関係者談）

これを利用した[pivot](https://github.com/implydata/pivot)というUIツールがあります。開発している会社は、[imply](http://imply.io)というサンフランシスコにあるベンチャー企業です。

この企業がduirdやpivotをパッケージにしたimplyというソフトウェアを提供しています。
今回は、これを使ってデータを分析してみます。

[http://imply.gepuro.net](http://imply.gepuro.net)にアクセスすると、当ブログで収集しているデータを見れます。（ただし、10月28日の分しか登録していません。早く全部入れたい・・・）

使い方については、少しずつ書いていきたいと思います。

