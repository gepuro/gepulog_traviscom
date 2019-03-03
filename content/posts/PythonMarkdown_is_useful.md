---
title: Python-Markdownが便利！
date: 2013-01-21T00:27:28+09:00
draft: false
tags: [Python]
aliases:
    - /archives/4
---

gepulogで、記事を書く際に利用しているライブラリの一つである[Python-Markdown](http://packages.python.org/Markdown/index.html)の紹介です。

Markdownというのは、文書を書くときに記述するマークアップ言語で、プレーンテキストをhtmlに変換してくれる優れものです。Pythonでの利用方法は、簡単で、
<pre>
\#!/usr/bin/python
\# -*- coding:utf-8 -*-
import markdown
text = u"プレーンテキスト"
print markdown.markdown(text)
</pre>
で、利用できます。すると出力は、
<pre>
``<p>プレーンテキスト</p>``
</pre>
のように出力されます。

書き方は、<http://daringfireball.net/>や<http://ja.wikipedia.org/wiki/Markdown>を参考にすると良さそうです。自分自身、まだ使い始めて間も無いので、調べながらでないと書けないけれども、慣れると便利になりそうですね。

