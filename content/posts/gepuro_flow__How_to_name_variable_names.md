---
title: gepuro流☆変数名の付け方
date: 2013-12-14T16:52:34+09:00
draft: false
tags: [R言語, イベント]
aliases:
    - /archives/80
---

[R Advent Calendar 2013](http://atnd.org/events/45043)の14日目担当のgepuroです。この記事の内容は、[Japan.R 2013](http://atnd.org/events/44682)の飛び入りLTで話した内容です。

### 変数名の付け方
irisというデータセットを例にします。
#### read.csv()をする時
ファイル名と変数名を同じにしておく。
ファイル名に日本語が含まれる場合は、英語にするかヘボン式で。
~~~{code}
iris <- read.csv("iris.csv")
~~~

#### subset()をする時
subsetした時の条件をドットで繋ぐ
~~~{code}
iris.setosa <- subset(iris, iris$Species == "setosa")

iris.SepalLength6m <- subset(iris, iris$Sepal.Length > 6)
iris.SepalLength6me <- subset(iris, iris$Sepal.Length >= 6)
iris.SepalLength6l <- subset(iris, iris$Sepal.Length < 6)

~~~
より大きいはmoreの"m"、より小さいはlessの"l"、イコールを含む時は、"e"を付ける。

####　mergeする時は、_で繋ぐ
~~~{code}
data1_data2 <- merge(data1, data2, by="id", all=T)
~~~
コードを見たまんまです。


#### データの形が変わったら、変数名を付け直す
~~~{code}
syukei <- table(iris$Species)
~~~
aggregate()やreshape()などを使うときも同様です。

#### 変数名が長くなってきて、コードが見づらくなったら、付け直す
なるべく変数名を付け直したくないけど、我慢できなくなる事がある。

### まとめ
自己流の変数名の付け方を紹介しました。これがベストだとは思っていないですが、他の人とコードを共有したり、自分でデータを解析していく時の戸惑いが少しは軽減できると思います。また、前処理のコードを別のファイルにしておく事もオススメです。

Google流のコーディングスタイルは、[Google's R Style Guide](http://google-styleguide.googlecode.com/svn/trunk/Rguide.xml)をご参照ください。


