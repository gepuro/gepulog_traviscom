---
title: 学務情報システムの単位修得状況照会ページでGPAを計算するブックマークレットを作った
date: 2014-09-08T22:27:22+09:00
draft: false
tags: [JavaScript]
aliases:
    - /archives/97
---

学務情報システムの単位修得状況照会ページでGPAを計算するブックマークレットを作った。
~~~~{.javascript}
javascript:var body = window.frames[2];var f = body.window.frameElement.valueOf();var ff = f.contentDocument.children[0];var result = ff.children[1].children[5].children[1];var sum = 0;var count = 0;for(i=0; i < result.childElementCount; i=i+1){var node = result.children[i];var record = node.children[9];str = record.textContent;if(str.search(/合/) > 0){sum += 0;count += 0;}else if(str.search(/不/) > 0){sum += 0;count += 1;}else if(str.search(/可/) > 0){sum += 1;count += 1;}else if(str.search(/良/) > 0){sum += 2;count += 1;}else if(str.search(/優/) > 0){sum += 3;count += 1;}else if(str.search(/秀/) > 0){sum += 4;count += 1;}} alert("GPA is " + sum/count);
~~~~
URLにコピペして実行してください。
計算が間違ってたら、ごめんなさい。

chromeだと動いてるようなのですが、firefoxだと動かない?

[プログラムソース簡単変換](http://filter.webings.net/)で改行コードを削除するのは、便利ですね。

