---
title: RからPostgreSQLへアクセスする
date: 2013-08-28T16:51:36+09:00
draft: false
tags: [R言語, PostgreSQL]
aliases:
    - /archives/53
---

<pre>
library("RPostgreSQL")
con <- dbConnect(PostgreSQL(), 
                 user= "username", 
                 password="password", 
                 dbname="dbname",
                 host="127.0.0.1",
                 port="5432")

rs <- dbSendQuery(con,
                  "select * from tablename")
data <- fetch(rs, n = -1)
</pre>
簡単ですね。


