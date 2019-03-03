---
title: github pagesにブログを引っ越しました
date: 2018-01-27T23:30:37+09:00
draft: false
tags: [blog, hugo]
---

2013年の1月にさくらVPSでブログを運用していましたが、github pagesに引っ越しをしました。ここ暫くはブログの更新が止まってしまっていたので気分転換をしたかったのと、VPSを安いプランに変更するための前準備です。

今回のブログは、

* [hugo](https://gohugo.io/)
* [github pages](https://pages.github.com/)
* [travis](https://travis-ci.org/)

を利用しています。

ブログのテーマは、[kiss](https://themes.gohugo.io/kiss/) を使っていて、一部に編集を加えています。hugoはmarkdownで記事を書くことが出来るのですが、前のブログもmarkdownで書いていたので、簡単な変換スクリプトを作成して移行しました。具体的には、

1. ページのurlを英字にするために、記事タイトルにgoogle翻訳をかけて変換
2. 日付の書式をhugoに合わせる
3. 前のブログで記事のurlが変わってしまうため、リダイレクトの設定をする。aliasesで解決
4. タグの設定方法をhugo形式に合わせる

です。


ブログの投稿はgithubのmasterブランチにmarkdownで書いた記事をpushすると、travisの自動ビルドが走り、githubのgh-pagesブランチにpushされます。github pagesには独自ドメインを設定することが出来るので、記事のurlを変えることなく移動できました。

## travisの設定

* .travis.yml を リポジトリの直下に置く
* ビルドのslack通知。 [図解: Travis CIの結果をSlack通知する方法](https://qiita.com/nwtgck/items/17840855cb76f60fa1fe)
* deployのfqdnに独自ドメインを指定する。ここでの設定がgithub pagesの設定に反映されます
* githubのtokenの設定, https://github.com/settings/tokens の　Personal access tokens　を取得して、travisの　https://travis-ci.org/gepuro/gepulog/settings にある Environment Variables に追加

slack通知用にtravisコマンドを入れた時に、ubuntuのaptからインストールしたのですが、バージョンが古いせいか上手く動きませんでした。 ( bash on ubuntu on windowsを使っています。 ) そのため、gemからインストールしました。この時のrubyのバージョンは2.0.0 です。ruby2.0-devとか色々足りなかったのでエラーメッセージを見ながらインストールしました。

~~~
language: bash

env:
    global:
      - HUGO_VERSION=0.32.1
      - GIT_URL=https://git@github.com/gepuro/gepulog.git

notifications:
  slack:
    secure: "TSEF4UkzYqZfzxxv398nzhmU7nP5OnrC6Sc+MJ7DhiWjSX+s846Msgwj6svzyBfp+FspYo7mXYC4faFgd2zod90g8XD9apmPQ9yBeP8+f2psap7PP3FtHney4OB6nFbFRsxo+Ue9YjXyMj6qycAgQ3pe7+ol8L+ZirxFVlYKoQUoE1nNElVtsVQro2D/j2UVWvV0ZttiSXQcO+AoF7+uEZAvGleGMXXR29u0AB7cLN11ASWhN8k4x5XyoBd7+L57GcgGs4OuBiUO1RB4SmoevdY+e5ozLoaoXUcw/n8TH59In4jz9ziVZ/9lMaItTblQMkKx4i9cDPCqfj0x9aKNhkYRM2uLHdL4zTPHTrwVHP/fW1p1sM+IU97cWH4/1RU9cntnUMewu3WDDNLzaapiK+4gV5d/AAVnaEoLzsXySHUfUT//Zi3h/DdcLLM+LTjfhOhxbfo2Hepnc6BYqYzeY8LSjUfL4TJpgV1wM/clnnOE0x/Gh8SNL2YvTaxzCj+2EZFF5uPPHsyWsIFaMG2+6hsnKNeRNrsSUUx5kARUN+rxfSVCJpp5ddb3x98lIrNvJ/oRSUy4iDR0xuTY+WJM142hqp5QPeQqriU9OjKNBCRU8wrfDLEuQEZTqgVjl+KrZTemBVlhk/f3jfNP2PgSnK6sjhgOhyYmG9I1uZTE+9Y="

install:
  - wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_Linux-64bit.tar.gz
  - tar zxvf hugo_${HUGO_VERSION}_Linux-64bit.tar.gz

before_deploy:
  - mkdir docs
  - ./hugo -t kiss-master

# Deploy to GitHub pages
deploy:
  provider: pages
  skip_cleanup: true
  local_dir: docs
  github_token: $GITHUB_TOKEN # Set in travis-ci.org dashboard
  fqdn: blog.gepuro.net
  on:
    branch: master
~~~

## github pagesの設定

1. ドメインの設定。 blog.gepuro.net の CNAMEを gepuro.github.io にする
2. https://github.com/gepuro/gepulog/settings の GitHub Pagesのsourceにgh-pages branchを指定する

## テーマの設定

* slideshare iconの追加。svgのことがよく分からないので苦戦しました。適当にいじっていたら目標通りになったので、こまかい挙動は分かってない
* kissのcssをインラインにした。googleのPageSpeed Insightsがいうには、それが良いらしい。

~~~
<style type="text/css">{{ readFile "/themes/kiss-master/static/css/style.css" | safeCSS }}</style>
~~~

* シンタックスハイライトの設定

config.tomlに以下を追加

~~~
pygmentsCodefences = true
pygmentsUseClasses = true
~~~

シンタックスハイライト用のcssの出力

~~~
$ hugo gen chromastyles --style=monokai > syntax.css
~~~

pre codeタグ内の背景と文字の色を変えるために、header.htmlに築城

~~~
pre, pre code {
    background-color: #000000;
    color: #00ff00;
    font-size: 15px;
~~~

* mathjaxの追加

最近に公式のcdnがなくなったらしい. [MathJax CDN shutting down on April 30, 2017. ](https://www.mathjax.org/cdn-shutting-down/)

~~~
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [['$','$'], ['\\(','\\)']],
      displayMath: [['$$','$$'], ['\[','\]']],
      processEscapes: true,
      processEnvironments: true,
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
      TeX: { equationNumbers: { autoNumber: "AMS" },
           extensions: ["AMSmath.js", "AMSsymbols.js"] }
    }
  });
</script>
  
<script type="text/javascript" src="https://cdn.rawgit.com/mathjax/MathJax/2.7.1/MathJax.js?config=TeX-AMS_HTML" defer> </script>
~~~


* rssのページ名の変更

config.tomlに追記

~~~
[outputs]
home = ["RSS", "HTML"]

[outputFormats]
[outputFormats.RSS]
mediatype = "application/rss"
baseName = "feed"
~~~

右上にRSSのリンクが表示されるようにnav.htmlに

~~~
<a class="level-item" href="{{ .RSSLink }}" target='_blank' rel='noopener'>
<span class="icon">
    <i class>
    {{- partial "svg/icons" "rss" -}}
    </i>
</span>
</a>
~~~

と追記したけど、もっと良い方法がありそう。


* favicon.icoをheader.htmlに追記

* slideshareのアイコンを右上に追加

theme.toml を以下を追記

~~~
slideshare = 'https://www.slideshare.net/'
~~~

config.tomlにtwitterやgithubに並んで、slideshareを追加


ここまでに、いくつも変更を加えてるので漏れているのがありそうですが、こんな感じです。
細かいところは、 https://github.com/gepuro/gepulog を見てもらえればと思います。


というわけで、ブログの引っ越しは終わりました。気になったところが出たら、また弄るつもりです。

