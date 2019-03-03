---
title: Djangoを始める
date: 2013-02-26T00:34:35+09:00
draft: false
tags: [Python, Django, product]
aliases:
    - /archives/18
---

[画像ベースのサイトを作ってみたいかもしれない ](http://blog.gepuro.net/archives/14) に向けて、[Django](https://www.djangoproject.com/)＆[PostgreSQL](http://www.postgresql.org/)でサイトを構築して行きたいと思う。

まずは、Djangoの利用の準備を始める。
データベースには、PostgreSQLの利用を想定する。

LinuxMintでのインストール方法
<pre>
$ sudo apt-get install python-django
</pre>

PostgreSQLのインストール
<pre>
$ sudo apt-get install postgresql
$ sudo apt-get install postgresql-client
</pre>

PostgreSQLのPython用のモジュール
<pre>
$ sudo apt-get install python-psycopg2
</pre>

<br>
#### プロジェクトを開始する
<pre>
$ django-admin startproject gepupic
</pre>
でプロジェクトを開始できる。

すると、gepupicというフォルダが作成され、
<pre>
gepupic/
    manage.py
    gepupic/
          \_\_init\_\_.py
          settings.py
          urls.py
          qsgi.py
</pre>
という構成でファイルが作成される。

開発用サーバーを起動する時は、
<pre>
$ python manage.py runserver
Validating models...

0 errors found
Django version 1.4.1, using settings 'gepupic.settings'
Development server is running at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>
というように起動する。
ここで、[http://127.0.0.1:8000](http://127.0.0.1:8000)にアクセスすると、
デモ画面が表示される。

次に、settings.pyの編集を行う
DATABASESの所を
<pre>
'ENGINE': 'django.db.backends.postgresql_psycopg2'
</pre>
として、あとは、NAME,USER,PASSWORD,HOST,PORTと設定を行う。
また、LANGUAGE_CODE='ja'とした。
ここで、PostgreSQLにてユーザとデータベースを作成しておく。
<pre>
$ createuser -a -d -U postgres -P ユーザー名
</pre>
でユーザを追加出来る。
また、
<pre>
$ psql
\> CREATE DATABASE gepupic;
</pre>
などと、データベースを作成する。これらの操作を行う時に、<code>su postgres</code>しておく必要があるかもしれない。

データベースの作成が終わったら、
<pre>
$ python manage.py syncdb
</pre>
と実行すると、データベースの設定が行われる。

モデルの作成は、
<pre>
$ python manage.py startapp apps
</pre>
とする。すると、
<pre>
apps/
    \_\_init\_\_.py
    models.py
    tests.py
    views.py
</pre>
が作成される。

今日は、このあたりで。
徐々に開発していきます。

参考:[はじめての Django アプリ作成、その 1](http://djangoproject.jp/doc/ja/1.0/intro/tutorial01.html)

