---
title: sparkの開発環境はspark-notebookが良さげ
date: 2015-10-02T16:00:13+09:00
draft: false
tags: []
aliases:
    - /archives/133
---

[spark-notebook](https://hub.docker.com/r/andypetrella/spark-notebook/)です。
何でもnotebookの時代ですね。

RStudio好きとしては、ブラウザで使えるIDEもあると嬉しい。

現時点での最新版は下で使える模様
~~~{sh}
docker pull andypetrella/spark-notebook:0.6.2-SNAPSHOT-scala-2.10.4-spark-1.5.0-hadoop-2.7.1-with-hive-with-parquet
docker run -p 9000:9000 -p 4040-4045:4040-4045 andypetrella/spark-notebook:0.6.2-SNAPSHOT-scala-2.10.4-spark-1.5.0-hadoop-2.7.1-with-hive-with-parquet 
~~~

scalaもsparkもスタート地点に立っていないレベルだけど、出来ることから少しずつ学ぶ。


