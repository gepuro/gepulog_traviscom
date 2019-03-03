---
title: PostgreSQLでnullの比較をする
date: 2013-09-04T22:32:31+09:00
draft: false
tags: [PostgreSQL]
aliases:
    - /archives/59
---

本日、陥った罠を忘れないために記録しておきます。

where句でnullが入った値を比較したい時に、<code><\></code>を用いると、正しく動作しない。なので、<code>is distinct from</code>を変わりに用いると良い。これは、nullが入っていない場合は、<code><\></code>と同じ動作をするようです。

参考:[9.2. 比較演算子](http://www.postgresql.jp/document/8.0/html/functions-comparison.html)

