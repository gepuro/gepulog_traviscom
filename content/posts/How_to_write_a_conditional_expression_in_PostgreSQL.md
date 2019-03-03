---
title: PostgreSQLに条件式の書き方
date: 2013-08-28T17:52:04+09:00
draft: false
tags: [PostgreSQL]
aliases:
    - /archives/55
---

case式を用いる。

<pre>
select a,
          case a when 0 then 'zero'
                     when 1 then 'one'
                     else a
          end
from table;
</pre>

すると、次のように得られる.


a | case
------------- | -------------
0  | 'zero'
1  | 'one'
2  | 2

参考:[6.12. 条件式](http://www.postgresql.jp/document/7.3/user/functions-conditional.html)

