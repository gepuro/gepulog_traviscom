---
title: AWS AthenaでPartitionの設定をする
date: 2017-02-16T00:31:29+09:00
draft: false
tags: [AWS]
aliases:
    - /archives/161
---

[AWS re:Invent 2016](https://aws.amazon.com/jp/about-aws/events/reinvent2016-1201/)で発表されたAthenaというサービスを試してみました。このサービスはS3上にあるデータに対してSQLでデータを取得することが出来ます。一言で言えば、AWS版のBigQueryです。
gzで圧縮されたままのデータに対してもクエリを投げることができ、データをスキャンした量に応じて課金されます。2017年2月15日現在では、1TB あたり5USDと非常に安いです。

BigQuery同様に巨大なデータをうっかりスキャンしてしまうとお金が飛んでいくので注意です。参考: [BigQueryで150万円溶かした人の顔](http://qiita.com/itkr/items/745d54c781badc148bb9)

というわけで、日付毎にPartitionを切りましょうという話です。
Englishが得意な皆さんは、[https://docs.aws.amazon.com/athena/latest/ug/partitions.html](https://docs.aws.amazon.com/athena/latest/ug/partitions.html)見てもらえば一発ですが、備忘録のために書き残します。

### データの準備
下にあるデータを仮定します。
~~~
$ cat 20170101.tsv 
a    1
a    2
a    3

$ cat 20170102.tsv 
b    1
b    2
b    3

$ cat 20170103.tsv 
c    1
c    2
c    3
~~~

### S3に保存する
こんな感じです。年月日ごとにログを保存してあるような、よく見る形式です。
~~~
$ aws s3 ls athenatest.aws.gepuro.net --recursive
2017-02-15 19:27:10          0 20170101/
2017-02-15 19:27:28         12 20170101/20170101.tsv
2017-02-15 19:27:15          0 20170102/
2017-02-15 19:27:42         12 20170102/20170102.tsv
2017-02-15 19:27:18          0 20170103/
2017-02-15 19:27:53         12 20170103/20170103.tsv
~~~

### テーブルを作る

GUIでの設定方法は見れば分かると思いますが、
次の通りです。

* データベース名: mytest
* テーブル名: ymdtest
* TSV形式
* データノカラムをvar1, var2という名前に
* Partitionのカラムをdtに
* S3のパス: s3://athenatest.aws.gepuro.net/

すると、次のようなクエリが生成されます。
~~~
CREATE EXTERNAL TABLE IF NOT EXISTS mytest.ymdtest (
  `var1` string,
  `var2` string 
) PARTITIONED BY (
  dt string 
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
WITH SERDEPROPERTIES (
  'serialization.format' = '    ',
  'field.delim' = '    '
) LOCATION "s3://athenatest.aws.gepuro.net/"
~~~

このクエリを実行すれば、テーブルが作られます。

### パーティション毎にデータを突っ込む
~~~
ALTER TABLE mytest.ymdtest add partition (dt="20170101")
LOCATION "s3://athenatest.aws.gepuro.net/20170101/"
~~~
とすると、20170101のデータをテーブルに突っ込めます。

これをパーティションだけ繰り返します。
つまり、

~~~
ALTER TABLE mytest.ymdtest add partition (dt="20170102")
LOCATION "s3://athenatest.aws.gepuro.net/20170102/"
~~~

~~~
ALTER TABLE mytest.ymdtest add partition (dt="20170103")
LOCATION "s3://athenatest.aws.gepuro.net/20170103/"
~~~

とすれば、データが入ります。

### クエリの実行

全データスキャンするなら、
~~~
SELECT * FROM mytest.ymdtest
~~~

20170101のみスキャンするなら、
~~~
SELECT * FROM mytest.ymdtest
WHERE dt='20170101'
~~~

20170101~20170102で範囲指定するなら、
~~~
SELECT * FROM mytest.ymdtest
WHERE
dt>='20170101'
AND dt<='20170102'
~~~

とします。

念押ししておくと、データスキャン量に応じて課金するので、
~~~
WHERE var1='a'
~~~
のように条件をつけただけでは、全データをスキャンしてしまいます。注意！！！
Athena画面上にスキャンした量が表示されるので、気にしておくと良いです。

また、クエリの出力結果はS3に保存されています。このバケットに対しては、ライフサイクルを設定しておいた方が良さそうです。

Enjoy Athena!!


