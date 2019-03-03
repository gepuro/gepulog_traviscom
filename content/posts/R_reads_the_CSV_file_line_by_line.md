---
title: RでCSVファイルを一行ずつ読み込む
date: 2015-08-08T00:01:27+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/127
---

<h3>メモリを節約するんじゃ</h3>
<p>学習データはサンプリングするからメモリに載せられるけど、 予測対象はメモリに乗らない時に一行ずつ読み込んで処理をさせたい。</p>
<pre class="{text}"><code>f &lt;- file(&quot;data.csv&quot;, &quot;r&quot;)
while(1){
  line &lt;- readLines(f, n=1)
  if(length(line) == 0){
    break
  }else{
    line.f &lt;- textConnection(line)
    line.df &lt;- read.csv(line.f, header = FALSE, sep=&quot;,&quot;)
  }
  write.table(line.df, stdout(), row.names=F, col.names=F, sep=&quot;,&quot;)
}</code></pre>
<p>標準入力から受け取る時は、</p>
<pre class="{text}"><code>file(&quot;data.csv&quot;, &quot;r&quot;)</code></pre>
<p>を</p>
<pre class="{text}"><code>file(&quot;stdin&quot;)</code></pre>
<p>にすれば良さそう</p>
</div>
<div class="section level3">
<h3>参考</h3>
<ul>
<li><a href="http://stackoverflow.com/questions/9370609/piping-stdin-to-r"><a href="http://stackoverflow.com/questions/9370609/piping-stdin-to-r" class="uri">http://stackoverflow.com/questions/9370609/piping-stdin-to-r</a></a></li>
</ul>




<h3> 追記 </h3>

奥村先生版です。

<blockquote class="twitter-tweet" data-partner="tweetdeck"><p lang="ja" dir="ltr">「RでCSVを1行ずつ」のいいかげん版：&#10;while (length((line = readLines(f,n=1))) &gt; 0) {&#10;  x = unlist(strsplit(line, &quot;,&quot;))&#10;<a href="https://t.co/fTpnA5CVIr">https://t.co/fTpnA5CVIr</a></p>&mdash; Haruhiko Okumura (@h_okumura) <a href="https://twitter.com/h_okumura/status/629825530510979072">August 8, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>


<h3> 追記2 </h3>

hoxo_mさんバージョンです。
[勝手に添削：RでCSVファイルを一行ずつ読み込む #rstatsj](http://qiita.com/hoxo_m/items/99853f973f38e8c240ee)

