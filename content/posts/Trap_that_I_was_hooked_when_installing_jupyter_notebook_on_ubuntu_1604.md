---
title: jupyter notebookをubuntu 16.04にインストールするときにハマった罠
date: 2016-10-04T15:01:12+09:00
draft: false
tags: [Python3]
aliases:
    - /archives/160
---

手元の端末に```jupyter notebook```をインストールしていなかったので、機嫌良く```pip3 install jupyter```でインストールして```jupyter notebook```で起動したところ、

~~~
Native kernel (python3) is not available
~~~

というメッセージが表示された。ブラウザから「New」を見てもPython3の文字は表示されていない。

改めてjupyterを次のようにしてインストールした
~~~
$ pip3 install --upgrade jupyter
~~~
すると、

~~~
Exception:
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/pip/req/req_install.py", line 1006, in check_if_exists
    self.satisfied_by = pkg_resources.get_distribution(str(no_marker))
  File "/usr/share/python-wheels/pkg_resources-0.0.0-py2.py3-none-any.whl/pkg_resources/__init__.py", line 535, in get_distribution
    dist = get_provider(dist)
  File "/usr/share/python-wheels/pkg_resources-0.0.0-py2.py3-none-any.whl/pkg_resources/__init__.py", line 415, in get_provider
    return working_set.find(moduleOrReq) or require(str(moduleOrReq))[0]
  File "/usr/share/python-wheels/pkg_resources-0.0.0-py2.py3-none-any.whl/pkg_resources/__init__.py", line 695, in find
    raise VersionConflict(dist, req)
pkg_resources.VersionConflict: (ipython 2.4.1 (/usr/lib/python3/dist-packages), Requirement.parse('ipython>=4.0.0'))

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/pip/basecommand.py", line 209, in main
    status = self.run(options, args)
  File "/usr/lib/python3/dist-packages/pip/commands/install.py", line 317, in run
    requirement_set.prepare_files(finder)
  File "/usr/lib/python3/dist-packages/pip/req/req_set.py", line 360, in prepare_files
    ignore_dependencies=self.ignore_dependencies))
  File "/usr/lib/python3/dist-packages/pip/req/req_set.py", line 448, in _prepare_file
    req_to_install, finder)
  File "/usr/lib/python3/dist-packages/pip/req/req_set.py", line 387, in _check_skip_installed
    req_to_install.check_if_exists()
  File "/usr/lib/python3/dist-packages/pip/req/req_install.py", line 1011, in check_if_exists
    self.req.project_name
AttributeError: 'Requirement' object has no attribute 'project_name'
~~~

というエラーが出ていることに気づいた。
どうやら、ipythonのバージョンが低いようなので、

```
pip3 install --upgrade ipython
```
をしてみたが変化なし。

どうやら原因は```apt-get```からインストールしたipythonのバージョンが低いようです。
手元の環境をよく見なおしてみると、```pip```からインストールしたipythonと```apt-get```からインストールしたipythonの両方が入っていました。

```apt-get```からインストールした古いipythonを削除して、再度jupyterをインストールしたら、無事に起動しました。

めでたし。めでたし。


