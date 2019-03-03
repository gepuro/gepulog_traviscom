---
title: GetNenshuJPというパッケージを開発しました
date: 2013-10-20T20:16:04+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/71
---

[http://cran.r-project.org/web/packages/GetNenshuJP/index.html](http://cran.r-project.org/web/packages/GetNenshuJP/index.html)にて公開されています。データは、[年収プロ](http://www.nenshu.jp/)というサイトからパースしています。

利用方法は、
~~~~{.R}
library(GetNenshuJP)
GetNenshuData("7203")
~~~~
とすれば、良いです。

~~~~{.text}
'data.frame':	11 obs. of  10 variables:
 $ code     : Factor w/ 1 level "7203": 1 1 1 1 1 1 1 1 1 1 ...
 $ name     : Factor w/ 1 level "トヨタ自動車": 1 1 1 1 1 1 1 1 1 1 ...
 $ year     : num  2012 2011 2010 2009 2008 ...
 $ period   : Factor w/ 11 levels "第100期 平成16年03月31日末",..: 10 9 8 7 6 5 4 3 2 1 ...
 $ employee : num  68978 69148 69125 71567 71116 ...
 $ temporary: num  9320 9139 8753 8725 14092 ...
 $ age      : num  38.6 38.3 38.3 37.9 37.8 37.1 37 37 36.7 36.9 ...
 $ seniority: num  15.5 15 15.3 15 15.1 14.2 14.5 15 14.8 15.4 ...
 $ income   : num  751 740 727 710 811 ...
 $ release  : Factor w/ 11 levels "2003/06/27","2004/06/24",..: 11 10 9 8 7 6 5 4 3 2 ...
~~~~
のように得られます。取得データは、

* code: 証券コード
* name: 会社名
* year: 決算年度
* period: 決算期
* employee: 従業員数
* temporary: 臨時従業員数
* age: 平均年齢
* seniority: 平均勤続年数
* income: 平均年収
* release: 情報開示日

となっています。

バグなどがありましたら、ご連絡下さい。


