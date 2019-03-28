---
title: ブログのタグを数える
date: 2019-03-29T00:23:00+09:00
draft: false
tags: [シェルスクリプト]
---

よく使っているタグが何かをシェルスクリプトで数えました。

```sh
cat * | grep tags | awk '{FS=": "}{print $2}' | sed -e 's/\[//g' | sed -e 's/\]//g' | sed -e 's/,/\n/g' | sed -e 's/\s//g' | sort | uniq -c | sort -n | tail
```


仕事ではPythonをメインに使ってますが、昔の記事の影響でR言語が多いですね。

```
6 Rstan
6 雑記
8 Python3
9 PostgreSQL
10 Web巡回メモ
14 統計
16 Python
23 イベント
27 Product
46 R言語
```

このカウントをする時に見つけた表記揺れは直しました。
