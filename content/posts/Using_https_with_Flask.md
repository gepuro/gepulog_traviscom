---
title: Flaskでhttpsを使う
date: 2013-03-01T22:40:47+09:00
draft: false
tags: [Python, Flask]
aliases:
    - /archives/24
---

認証を使うようなサービスは、SSLを用いてセキュアな通信をしたい。
Flask自身が持ってるwebサーバでは、SSLが使えないので、
apacheやwsgiの力を借りる必要がある。

まず、オレオレ証明書を作る。

<pre>
$ openssl genrsa -aes128 1024 > server.key
$ openssl req -new -key server.key > server.csr
$ openssl x509 -in server.csr -days 365 -req -signkey server.key > server.crt
</pre>
とこんな感じで作れる。
作成したものは、それぞれ

* server.key ：秘密鍵
* server.csr  ：公開鍵
* server.crt   ：デジタル証明書

である。

apacheの設定は、SSLの方は、
<pre>
LoadModule ssl_module modules/mod_ssl.so
\#Listen 443
AddType application/x-x509-ca-cert .crt
AddType application/x-pkcs7-crl    .crl
SSLPassPhraseDialog builtin
SSLSessionCache     shmcb:/var/cache/mod_ssl/scache(512000)
SSLSessionCacheTimeout 300
SSLMutex default
SSLRandomSeed startup file:/dev/urandom 256
SSLRandomSeed connect builtin
SSLCryptoDevice builtin

&lt;VirtualHost *:443>
ServerName example.com
WSGIScriptAlias / /home/hogehoge/apps/hello.wsgi
SSLEngine On
SSLProtocol all -SSLv2
SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:RC4+RSA:+HiGH:+MEDIUM:+LOW
SSLCertificateFile /home/hogehoge/apps/server.crt
SSLCertificateKeyFile /home/hogehoge/apps/server.key

&lt;Directory /home/hogehoge/apps>
WSGIProcessGroup hello
WSGIApplicationGRoup %{GLOBAL}
WSGIScriptReloading On
Order deny,allow
Allow from all
&lt;/Directory>

&lt;/VirtualHost>

</pre>
となる。
通常のhttpの方は、
<pre>
&lt;VirtualHost *:80>
ServerName example.com
WSGIDaemonProcess hello user=www-data group=www-data
WSGIScriptAlias / /home/hogehoge/apps/hello.wsgi
&lt;Directory /home/hogehoge/apps>
WSGIProcessGroup hello
WSGIApplicationGRoup %{GLOBAL}
Order deny,allow
Allow from all
&lt;/Directory>
&lt;/VirtualHost>
</pre>
とした。

サービス本体は、
hello.py
<pre>
\#!/usr/bin/python
\# -+- coding:utf-8 -*-

from flask import Flask
app = Flask(\_\_name\_\_)

@app.route("/")
def hello():
    return "Hello World!"

if \_\_name\_\_ == "\_\_main\_\_":
    app.run()
</pre>
また、hello.wsgiは、
<pre>
\#!/usr/bin/python
\# -+- coding:utf-8 -*-
import sys
sys.path.insert(0, '/home/hogehoge/apps')

from hello import app as application
</pre>
としておく。

これで、https://127.0.0.1/ でアクセス出来るようになった。

参考：

* [Apache/SSL自己証明書の作成とmod sslの設定](http://www.maruko2.com/mw/Apache/SSL%E8%87%AA%E5%B7%B1%E8%A8%BC%E6%98%8E%E6%9B%B8%E3%81%AE%E4%BD%9C%E6%88%90%E3%81%A8mod_ssl%E3%81%AE%E8%A8%AD%E5%AE%9A)
* [flask, mod_wsgi, ssl, oh my!](http://www.subdimension.co.uk/2012/11/10/flask_modwsgi_ssl_oh_my.html)


