---
title: "awscliを使おうとしたらget_environ_proxies() missing 1 required positional argument: no_proxyと怒られた話"
date: 2017-05-12T14:06:55+09:00
draft: false
tags: []
aliases:
    - /archives/162
---

ある日、いつも通りに


```
$ aws s3 ls
```

と実行したら、

```
get_environ_proxies() missing 1 required positional argument: 'no_proxy'
```

と怒られました。

手元の環境は、Ubuntu16.04で、 awscliのバージョンは  aws-cli/1.11.13 Python/3.5.2 Linux/4.4.0-75-generic botocore/1.4.70 です。

エラーメッセージでググッても何も出てこないので、再起動をしてみたり、awscliをダウングレードしたりしましたが、結果は変わらず。

最終的には、

```
$ sudo pip3 install --upgrade boto3
```

と実行して、boto3のバージョンを最新にあげたら直りました。

めでたし、めでたし。
