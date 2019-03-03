---
title: PostgreSQLにおけるキャストの方法
date: 2013-08-28T17:03:31+09:00
draft: false
tags: [PostgreSQL]
aliases:
    - /archives/54
---

2種類の書き方があるみたい。
<pre>
cast(hoge as integer)
</pre>
か

<pre>
hoge::integer
</pre>

で出来る。個人的には、後者の方が好きかな。

データの型は、公式ドキュメントに纏められている。

参考: [第8章　データ型](http://www.postgresql.jp/document/9.1/html/datatype.html)


