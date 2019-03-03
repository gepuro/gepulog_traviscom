---
title: PythonからPostgreSQLにアクセスする
date: 2013-08-29T16:12:17+09:00
draft: false
tags: [Python, PostgreSQL]
aliases:
    - /archives/56
---

[psycopg2](https://pypi.python.org/pypi/psycopg2)を使うと便利なようです。
以下は、最低限必要になりそうなサンプル。
<pre>
import psycopg2
con = psycopg2.connect(
	database = "database"
	, user = "username"
	, password = "password"
	, host = "host"
	, port = 5432
	)
cur = con.cursor()
cur.execute("select * from tablename")
cur.fetchall()
</pre>

