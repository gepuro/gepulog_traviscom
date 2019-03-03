---
title: テーブル一覧を取得
date: 2013-10-03T09:08:24+09:00
draft: false
tags: [PostgreSQL]
aliases:
    - /archives/62
---

コンソール上で
<pre>
$ psql mydb
</pre>
などのように接続したあと、
<pre>
\d
</pre>
と入力するか、

<pre>
select * from pg_tables where not tablename like 'pg%' order by tablename;
</pre>
とクエリを投げると、DB上のテーブル名を取得できる。


