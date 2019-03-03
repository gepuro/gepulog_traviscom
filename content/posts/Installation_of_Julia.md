---
title: Juliaのインストール
date: 2013-11-10T02:13:09+09:00
draft: false
tags: [JuliaLang]
aliases:
    - /archives/75
---

ubuntu13.10で、ソースからコンパイルしてみました。

~~~{.sh}
$ git clone https://github.com/JuliaLang/julia.git
$ cd julia
$ make
~~~

時間がかかるので、バイナリでインストールするのが良いです。
i7-3770でコンパイルをすると、40分程の時間が必要です。


~~~{.julia}
gepuro@ubuntu$ julia 
               _
   _       _ _(_)_     |  A fresh approach to technical computing
  (_)     | (_) (_)    |  Documentation: http://docs.julialang.org
   _ _   _| |_  __ _   |  Type "help()" to list help topics
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 0.2.0-rc3+37 (2013-11-09 06:29 UTC)
 _/ |\__'_|_|_|\__'_|  |  Commit 287ace1 (0 days old master)
|__/                   |  x86_64-linux-gnu

julia> 1 + 1
2

julia> 2^10
1024

julia> print("hello")
hello
~~~

こんな感じに動作確認しました。


