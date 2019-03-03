---
title: Python3のFlaskをwsgiで動かす
date: 2016-01-22T23:37:58+09:00
draft: false
tags: [Python3, Flask]
aliases:
    - /archives/148
---

自分の環境ではdocker上で動かしていましたが、本筋には影響を与えないので省略します。

Flaskのドキュメントである[mod_wsgi (Apache)](http://flask.pocoo.org/docs/0.10/deploying/mod_wsgi/)に書いてあるし、Python2の頃は試したことあるから、余裕だろうと油断していた。

~~~
sudo apt-get install -y apache2 \
    apache2-mpm-prefork \
    apache2-utils \
    libexpat1 \
    ssl-cert \
    libapache2-mod-wsgi-py3
~~~

上のようにインストールした。
また、

/etc/apache2/sites-available/000-default.conf
を

~~~
<VirtualHost *:80>
ServerName example.com
WSGIDaemonProcess app user=www-data group=www-data
WSGIScriptAlias / /app/app.wsgi
<Directory /app>
WSGIProcessGroup app
WSGIApplicationGRoup %{GLOBAL}
# Order deny,allow
# Allow from all
Require all granted
</Directory>
</VirtualHost>
~~~

にします。dockerで動かすだけなので、ファイルを直に編集しています。


### ハマりポイント1
<code>libapache2-mod-wsgi-py3</code>をインストールする。

パッケージ名から想像できるが、wsgiはPython3とPython2系で異なる。

### ハマりポイント2

apacheの設定では、
<code>Require all granted</code>を使う。

Python2系では、<code>
Order deny,allow
Allow from all
</code>で良かった。

[http://stackoverflow.com/questions/30642894/getting-flask-to-use-python3-apache-mod-wsgi](http://stackoverflow.com/questions/30642894/getting-flask-to-use-python3-apache-mod-wsgi)に救われました。


ちなみにPyttho2系の時は、[Flaskでhttpsを使う](http://blog.gepuro.net/archives/24)に書きましたので参考にどうぞ。



