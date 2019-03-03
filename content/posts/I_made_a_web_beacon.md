---
title: webビーコンを作った
date: 2015-09-14T22:24:23+09:00
draft: false
tags: [Python]
aliases:
    - /archives/131
---

google analyticsでアクセスログを取ってるのだけれど、セッション毎のデータを利用するのは有料だったので、自分で作ってみた。

<script src="https://gist.github.com/gepuro/ddd28f55d78abec9b953.js"></script>

な感じのプログラムに対して、
<pre><code>&lt;script type=&quot;text/javascript&quot;&gt;document.write(&quot;&lt;img src=&#039;http://beacon.gepuro.net/?site=blog.gepuro.net&quot;);document.write(&quot;&amp;referrer=&quot;+encodeURIComponent(document.referrer)+&quot;&#039;&gt;&quot;);&lt;/script&gt;</code></pre>
な感じにタグを埋め込んで使う。

このサイトでデータを集めて、何かに使うつもり。

