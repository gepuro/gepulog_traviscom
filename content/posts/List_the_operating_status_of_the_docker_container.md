---
title: dockerコンテナの稼働状況を一覧する
date: 2015-12-18T22:00:35+09:00
draft: false
tags: [docker]
aliases:
    - /archives/140
---

コンテナの稼働状況は下のようにして確認出来る。
~~~{.sh}
docker stats [OPTIONS] CONTAINER [CONTAINER...]
~~~

しかしながら、コンテナ名orIDを入力するのは面倒ってわけで、一発で表示できるコマンドを作った。
~~~{.sh}
#!/bin/sh
docker inspect --format='{{.Name}}' $(docker ps -q) | sed -e "s/\///g" | xargs docker stats
~~~

また、コンテナのipアドレスを調べたい時は、

~~~{.sh}
#!/bin/sh
docker inspect --format '{{.Name}},{{.NetworkSettings.IPAddress}}' $(docker ps -q) | sed -e "s/\///g"
~~~
とすれば良い。

これをpathの通る場所に置いておけば、dockerライフがより素敵になる！！！

