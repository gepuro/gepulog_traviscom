---
title: Dockerでapacheを使う
date: 2016-01-24T17:03:26+09:00
draft: false
tags: [docker]
aliases:
    - /archives/149
---

忘れそうなので、メモしておく。
~~~
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV APACHE_RUN_DIR /var/run/apache2
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2

CMD ["/usr/sbin/apache2", "-D", "FOREGROUND"]
~~~

