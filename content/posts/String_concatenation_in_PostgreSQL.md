---
title: PostgreSQLにおける文字列の連結
date: 2013-09-04T23:12:04+09:00
draft: false
tags: [PostgreSQL]
aliases:
    - /archives/61
---

文字列の連結は、
<pre>
hoge || poyo
</pre>
とする。
<pre>
(hoge || poyo) as mozi
</pre>
のようにして、列名をつける事が出来るし、
<pre>
(hoge || poyo)::Date
</pre>
のようにして、型変換を行うことも出来る。


