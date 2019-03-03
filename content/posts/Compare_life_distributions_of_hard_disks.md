---
title: ハードディスクの寿命分布を比較
date: 2015-02-07T20:27:46+09:00
draft: false
tags: [R言語, 統計]
aliases:
    - /archives/118
---

[Hard Drive Data Sets](https://www.backblaze.com/hard-drive-test-data.html)に4万台以上のハードディスクに関するデータが公開されている。これらの寿命分布を推定してみようと思う。

### 公開されているデータをSQLiteに展開し、前処理
~~~{sql}
create table total_days as
select serial_number, model, count(*) as total_days,
max(failure) as failure,
min(date(date)) as min_date, max(date(date)) as max_date
from drive_stats
group by serial_number
;
~~~

### Rで読み込み、解析準備
~~~{r}
library(RSQLite)
library(dplyr)

drv<-dbDriver("SQLite")
con<-dbConnect(drv,dbname="drive_stats.db")
totaldays <- dbGetQuery(con,"select * from total_days")
head(totaldays)

totaldays %>%
  group_by(model) %>%
  summarise(model_sum = n()) %>%
  arrange(model_sum)

hdd.df <- 
  totaldays %>%
  filter(model %in% c("ST4000DM000", "HGST HMS5C4040ALE640"))

weib.df <- data.frame(
  x = hdd.df$total_days,
  condition = ifelse(hdd.df$model == "ST4000DM000", 1, 2),
  status = hdd.df$failure
  )
~~~

### ワイブルプロットでパラメータの推定
![ワイブルプロット](https://www.dropbox.com/s/qmgrvyt1t971c37/hdd_weibull.png?dl=1)

 * 青色 : ST4000DM000, mhat = 0.7875, etahat = 3.550e+04
 * 赤色 : HGST HMS5C4040ALE640, mhat = 0.7628, etahat = 1.193e+05

です。

平均寿命で比較すると、
ST4000DM000は40685日で、HMS5C4040ALE640は140100日だった。

また、B1ライフ(1%壊れるまでの日数)は、
ST4000DM000は103.1日で、HMS5C4040ALE640は286.8日だった。

### 確率密度関数をプロット
![pdf](https://www.dropbox.com/s/222mbqgtpem9yh7/hdd_pdf.png?dl=1)


こんな感じでHDDの寿命をモデル毎に比較出来る。(≧∇≦)/

今回対象としたモデルは、データ数が多い2つに注目したわけだが、こんなにも違いが出るなんて驚いた。

