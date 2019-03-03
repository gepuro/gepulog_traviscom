---
title: 『詳解 Apache Spark』出版記念イベントに参加しました
date: 2016-05-11T20:50:41+09:00
draft: false
tags: [Spark, イベント]
aliases:
    - /archives/151
---

最近はSparkに触れていなかったのですが、日本語の情報も豊富になってきたのを感じました。
自分も書きたい。
ブログネタでやるかなあ。と思った一日でした。

### データ分析業界の理想と現実 - Spark普及の歴史を添えて（下田倫大さん）

* Spark登場時に私は夢とロマンを見ました！
* 現実は厳しい・・・。
* ガンガン使おう
* 2013年: 大規模データ処理といえばHadoopで集計
* 2014年: Sparkとの出会い, インドで使ってた
* 第一印象: Python, Rのインターフェースがある！
* 2015年: ブレインパッド社としてビジネスモデル的にSparkのメリットが大きくない
* コンサル/SI は新しい技術導入が遅くなりがち
* ニーズが必要
* DeltaCubeという自社開発プロダクトで導入
* 案件が効率化からイノベーションに変化

### データマネジメントツールDeltaCubeでのSpark利用（師岡一成さん）

* 2014年に触り始めた
* 2015年に本格的に
* DeltaCube: プライベートDMPのログからセグメントを作成ツール
* 2014年に開発が始まり、リリースまで2年ぐらいかかった
* 初期
    * 行動ログを手動で検索して、セグメントを作成
    * データサイズは数TB, presto, impalaを使った
* 中期(2015年)
    *　機能的にマニアック路線
    * 広告運用の手間がかからないようにしたい
    * 自動でユーザをクラスタリング(DeltaCube)
    * Rtosterで自動ABテスト
    * ETL, MLlib
* 現在
    * YARNとPrestoの二つはいやだけど、統一できず・・・
    * EMR上にPrestoクラスタを稼働
* Sparkよ流行ってくれ


### Dynamic Resource Allocation　in Apache Spark（今井雄太さん, @imai_factory）

* Sparkとの出会い
    * Amazon KinesisのConsumerとして
* Spark運用の話
* ワードカウントの例(scalaで)
* DAG Scheduler
    * sc.parallelize()でRDDが出来る、分散して保持されるオブジェクト
    * Array⇒ParallelCollectionRDD⇒MapPartitionsRDD⇒ShuffledRDD⇒Array
* Tasks > Executors
    * Executorsの数をダイナミックに変えてくれる
    * Tasksの応じて、増やしたり、減らしたり
* Dynamic Resource Allocationのよさ
    * 投げられたJobに対して、動的にExecutorsを変えて、速度を上げる
    * 消さないとリソースが無駄になるので、消そう

### Help me! Help me with DNN in Spark!!（石川 有さん）

* Spark2.0に向けてDNNの拡張を進めるとあったが、見送られた
* 誰か助けて！
* MultilayerPerceptronClassifier
    * Spark1.5
    * L-BFGS
    * Sigmoid, Softmaxのみ
* Regression対応が必要
    * ReLU, 恒等写像
    * L-BFGSではなくSGDに最適化アルゴリズムを変更する必要がある
    * AdaGrad, Adamなどを実装できるように更新処理を再設計
* 現在のSGDは非効率で精度が悪い
    * Parallelized SGDとして再実装する必要がある
* Drop-outもできない
* CNNのような多次元配列の入力をサポートしていない
* Bayesian Optimizationなどの自動チューニングアルゴリズムを追加するべき
* 既存のPublic APIを変更するのを嫌う風土


### scalatestでSpark StreamingのUnitを書く話（田中裕一さん）

* 100万人のデータ分析(Sparkを含む)
* API 300億回/日でもSparkが使える
* Spark as a Service
* StreamingのUnitが面倒
    * アプリケーション側で状態を保つため、Unitが組みづらい
    * Streamingは外部との接続が前提なためUnitが書きにくい
    * 複数のmicro-batchの集計結果などの書き方が難しい(window処理など)
    * やったこと
        * Unit側でStream作成、Unitでテストデータ作成⇒Streamへ、処理結果の出力
    * 処理部分と接続部分は切り分けて書く(出力部分の切り分けた方が良い)
    * どうやってStreamを渡すか
* SparkでTestを書こう
* 簡単に書けるけど、構造化を意識しないと汚いコードになっちゃうので注意

### spark.ml の API で XGBoost を扱いたい！（小宮篤史さん）

* SmartNewsのエンジニアをしてます
* XGBoostを知っているひと⇒　少数
* Spark上でXGBoostを使ったことあるひと⇒　会場でほぼいない
* SparkXGBoost
    * pure Scalaで実装
    * 開発が活発ではない
* xgboost4j-spark
    * DMLCが提供する公式のSpark integration
    * RDDのみ、DataFrameに非対応

<script async class="speakerdeck-embed" data-id="4a98fb59620d4ef59c5579d14f3855bd" data-ratio="1.33333333333333" src="//speakerdeck.com/assets/embed.js"></script>


### Sparkで始めるお手軽グラフデータ分析（加嵜長門さん）

* Hadoop/Spark Conference Japan 2016でのアンケート
* GraphXの利用ユーザが30人！(Spark SQL/ DataFrameは252人)
* GraphFramesの登場
* GraphXとDataFramesの統合
* 1人3冊買えば、続編が出るかも！
* グラフじゃないとできないの？
    * 多くの場合は他の選択肢がある
* いろんな視点を持つことが大事
* 社内営業をする時に、絵で説明できて良いこともある
* リコメンド
    * ユーザ同士、商品間
* マーケティング
    * ネットワークビジネス
    * バイラルマーケティング
    * 口コミを使ったマーケティング
        * 影響力の強いユーザの抽出
* 不正検知
    * 偽装保険金詐欺
        * 少人数で何度も事故にあっているなんてことが・・・
    * クレジットカードの詐欺
        * 住所や電話番号を少人数で使いまわす
* 会社間のお金の流れ
* グラフDB
    * Neo4J, Titan
    * 分散処理による高スループット
* グラフ処理系
    * GraphLab
    * グラフに特化しない汎用的なデータ構造
    * 表形式やベクトルとシームレスに結合できる
* RDDから1行でグラフを作れる！


### さいごに

アフィリエイトを張っておきます。

<iframe src="http://rcm-fe.amazon-adsystem.com/e/cm?t=gepuro-22&o=9&p=8&l=as1&asins=4774181242&ref=qf_sp_asin_til&fc1=000000&IS2=1&lt1=_blank&m=amazon&lc1=0000FF&bc1=000000&bg1=FFFFFF&f=ifr" style="width:120px;height:240px;" scrolling="no" marginwidth="0" marginheight="0" frameborder="0"></iframe>


