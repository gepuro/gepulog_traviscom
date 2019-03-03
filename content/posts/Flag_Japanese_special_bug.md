---
title: Flaskの日本語特のバグ?
date: 2013-10-16T20:20:12+09:00
draft: false
tags: [product, Flask]
aliases:
    - /archives/70
---

~~~~{.python}
@app.route("/hoge/<fuga>", defaults={"page":1})
@app.route("/hoge/<fuga>/", defaults={"page":1})
@app.route("/hoge/<fuga>/<int:page>")
~~~~
などのようにルーティングを指定した時に、

OK

* /hoge/日本語
* /hoge/日本語/
* /hoge/日本語/2

NG

* /hoge/日本語/1

となっている。
ルーティングの部分で問題になっているような気がするのだが、もしかしたら他の部分かもしれない。調査が終了したら、報告したいな。


