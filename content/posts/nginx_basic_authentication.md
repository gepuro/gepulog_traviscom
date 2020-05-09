---
title: SSLでBasic認証付きなサイトをサクっと作る方法
date: 2020-05-09T23:00:00+09:00
draft: false
tags: [Docker, Nginx, SSL, Basic認証]
---

docker-compose内で http://hoge:3000/ にてサービスが立ち上がっていることを前提に書いています。

## Nginx, https-portal: docker-compose.yml
おなじみのnginxと、https-portal( https://github.com/SteveLTN/https-portal ) という　Let's Encryptを簡単に使えるようにdocker化されたイメージを使う。

```
version: '3'
services:
  https-portal:
    image: steveltn/https-portal:1
    ports:
      - '80:80'
      - '443:443'
    links:
      - nginx
    restart: always
    environment:
      DOMAINS: 'example.com -> http://nginx:8080'
      STAGE: 'production'
      # STAGE: 'local'
      # FORCE_RENEW: 'true'
    volumes:
      - ./https-portal/https-portal:/var/lib/https-portal
      - ./https-portal/vhosts:/var/www/vhosts
  nginx:
    image: nginx:latest
    container_name: 'nginx'
    volumes:
      - ./conf.d/server.conf:/etc/nginx/conf.d/server.conf
      - ./conf.d/.htpasswd:/etc/nginx/.htpasswd
    # ports:
    #   - "80:80"
```

## nginxの設定ファイル server.conf
```
server {
    listen       8080;
    server_name  example.com;
    proxy_set_header    Host    $host;
    proxy_set_header    X-Real-IP    $remote_addr;
    proxy_set_header    X-Forwarded-Host       $host;
    proxy_set_header    X-Forwarded-Server    $host;
    proxy_set_header    X-Forwarded-For    $proxy_add_x_forwarded_for;

    location / {
        auth_basic "Restricted";                   # 認証時に表示されるメッセージ
        auth_basic_user_file /etc/nginx/.htpasswd; # .htpasswdファイルのパス
        proxy_pass   http://hoge:3000/;
    }

}
```

## .htpasswdを生成
```
$ sudo htpasswd -c ./conf.d/.htpasswd username
```

## 起動
docker-composeして終わり
```
docker-compose up
```
