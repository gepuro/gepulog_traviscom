---
title: ccchartで簡単に綺麗なグラフを描く
date: 2013-07-26T21:20:11+09:00
draft: false
tags: [web, JavaScript, 可視化]
aliases:
    - /archives/42
---

[ccchart](http://ccchart.com/index.htm)というJavaScriptでグラフを描画することが出来るライブラリが手軽に綺麗なグラフを描けそうです。

以下は、サイトに載っていたサンプルです。

<script src="http://blog.gepuro.net/javascript/ccchart.js" charset="utf-8"></script>
<canvas id="ccchart_sample"></canvas>
<script>
var chartdata68 = {

  "config": {
    "title": "Option markerWidth for useMarker: css-ring",
    "subTitle": "css-ringのリング直径。デフォルト10",
    "type": "line",
    "useMarker": "css-ring",
    "lineWidth": 1,
    "borderWidth": 4,
    "markerWidth": 15
  },

  "data": [
    ["年度",2007,2008,2009,2010,2011,2012,2013],
    ["紅茶",435,332,524,688,774,825,999],
    ["コーヒー",600,335,584,333,457,788,900],
    ["ジュース",60,435,456,352,567,678,1260],
    ["ウーロン",200,123,312,200,402,300,512]
  ]
};

ccchart.init("ccchart_sample", chartdata68);

</script>

使いこなせると便利だと思うのでした。


