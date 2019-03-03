---
title: python3による日付の扱い方メモ
date: 2015-03-28T00:44:34+09:00
draft: false
tags: [Python3]
aliases:
    - /archives/121
---

### twitterのAPIによるとタイムスタンプ
<pre>
Wed Dec 24 11:34:28 +0000 2014
</pre>

### python3で処理する
~~~{.python}
from datetime import datetime, timezone, timedelta
time = "Wed Dec 24 11:34:28 +0000 2014"
d = datetime.strptime(time, '%a %b %d %H:%M:%S %z %Y')
~~~
とすると、dは
~~~{.python}
datetime.datetime(2014, 12, 24, 20, 34, 28, tzinfo=datetime.timezone(datetime.timedelta(0, 32400), 'JST'))
~~~
となる。

### unixtimeを得る

~~~{.python}
d.timestamp()
~~~
とすればよく、結果は
<pre>
1419420868.0
</pre>
となる。

### 日本時間にする
~~~{.python}
JST = timezone(timedelta(hours=+9), 'JST')
d = datetime.fromtimestamp(d.timestamp(), JST)
~~~
とする。

###  文字列として出力
~~~{.python}
d.strftime("%Y/%m/%d %H:%M:%S")
~~~
で出力は
<pre>
'2014/12/24 20:34:28'
</pre>
となる。



この辺りの処理って、すぐに忘れるよね。汗


### 参考
 * [Converting Twitter's date format to Datetime in python](http://stackoverflow.com/questions/18711398/converting-twitters-date-format-to-datetime-in-python)
 *  [Python 3 で少しだけ便利になった datetime の新機能](http://qiita.com/methane/items/d7ac3c9af5a2c659bc51)


