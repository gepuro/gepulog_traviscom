---
title: "ioslidesにおけるロゴのサイズを変える"
date: 2015-12-02T08:48:16+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/138
---

スライドをRmdで書くgepuroです。

デフォルトのままでは、ロゴのサイズがピクセル単位で固定されているので、画像によっては歪んで表示されてしまう。

それでは困るので、 CSSを弄りました。

また、

タイトル以外のページに現れるロゴは邪魔なので、

<code>background-size: 0px 0px;</code>として、消しています。

### slide.Rmd

~~~{r}

title: "タイトル"

author: "@gepuro"

output_format:

  ioslides_presentation:

    css: logo.css

    logo: logo.png

~~~



### mystyle.css

~~~{css}

/* トップのロゴの大きさを変える */

aside.gdbar img {

width: 180px;

height: 47.2px;

position: absolute;

right: -5px;

top: 15px;

margin: 8px 15px;

}



/* 他のページのロゴの大きさを変える */

slides > slide:not(.nobackground):before {

font-size: 12pt;

content: "";

position: absolute;

bottom: 20px;

left: 60px;

background: url(logo.png) no-repeat 0 50%;

-webkit-background-size: 30px 30px;

-moz-background-size: 30px 30px;

-o-background-size: 30px 30px;

background-size: 0px 0px;

padding-left: 40px;

height: 30px;

line-height: 1.9;

}

~~~
