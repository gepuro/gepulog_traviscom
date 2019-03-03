---
title: memcachedを導入した
date: 2013-08-08T02:15:25+09:00
draft: false
tags: [product, Flask]
aliases:
    - /archives/48
---

[http://flask.pocoo.org/docs/patterns/caching/](http://flask.pocoo.org/docs/patterns/caching/)を参考にして、memcachedをgepulogに導入しました。

<pre>
from werkzeug.contrib.cache import MemcachedCache
cache = MemcachedCache(['127.0.0.1:11211'])
</pre>
としておいて、
<pre>
cache.get("key")
cache.set("key", "value", timeout=5*60)
cache.delete("key")
</pre>
などのようにして使うことが出来る。

サーバのメモリはまだ余裕があるので、有効活用できるかな？


