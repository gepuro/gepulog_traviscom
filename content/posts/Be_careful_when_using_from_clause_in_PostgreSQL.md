---
title: PostgreSQLでfrom句を使う時に気をつける事
date: 2013-09-04T23:04:20+09:00
draft: false
tags: [PostgreSQL]
aliases:
    - /archives/60
---

from句で、テーブルを複数選択したい時に
<pre>
from table1,table2
</pre>
や
<pre>
from (select * from table1) as t1,
        (select * from table2) as t2
</pre>
とするのは良いが、

<pre>
from table1, (select * from table2) as t2
</pre>
が出来ないと思ってたが、出来た。(twitterで教えて頂きましたので、修正しました。)

作業している時に、サブクエリに関するエラーが出ていたのですが、手元で動かしても再現出来ないので、再び出会ったら記事にします。（もしかしたら、エラーメッセージを勘違いしてたのかも）


