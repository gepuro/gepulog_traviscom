---
title: オススメのPython開発環境 - 2020年3月編
date: 2020-03-01T14:00:00+09:00
draft: false
tags: [Python]
---

Pythonの開発環境を整理します。
基本的なコンセプトは自分自身での設定をなるべく減らすです。

## エディタ: VS Code
Sublime Text、PyCharmと使ってきましたが、VS Codeが圧倒的に楽。初期状態である程度は便利に使えて、お気に入り機能も自分で追加することが出来る。

Pythonを書く時に役立ってるエクステンションは次の２つです。

- ms-python.python : Microsoftが提供する公式エクステンション
- gruntfuggly.todo-tree : コメントにTODO, FIXMEとかけば、修正予定の箇所を一覧で見える

## Poetry
Pythonの環境構築で流行りのツール。venvやpipenvと経由して、Poetryに至る。
以下のようにして環境構築する。

```
$ brew update
$ brew install pyenv

$ cat << 'EOS' >> ~/.bash_profile
# pyenvに~/.pyenvではなく、/usr/loca/var/pyenvを使うようにお願いする
export PYENV_ROOT=/usr/local/var/pyenv

# pyenvに自動補完機能を提供してもらう
if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi

EOS

$ source ~/.bash_profile

$ pyenv install 3.8.0
$ pyenv global 3.8.0
$ pyenv versions
3.8.0が*印がついており、選択されているのを確認

$ python -m pip install pip --upgrade
$ pip install --user poetry

$ cd ~/hogehuga
$ poetry init # 新たなプロジェクトを作る場合
$ poetry install # 既存の設定を使って、ローカルに展開する場合
```

あと、Poetryで覚えておくコマンドは、

```
$ poetry add ライブラリ名==バージョン
```

```
$ poetry run python run.py
```

```
$ poety shell
```
ぐらいかと思います。


## mypy
動的型付け言語は書きやすいですが、自由度が高すぎるのがたまにキズ。コメントを残しておかないと読みやすさが低下してしまいそうな箇所には、typehintを使って型の情報を教えてあげると読みやすさとコーディングのしやすさが向上します。最初は面倒に感じていましたが慣れてくると快適と思えてきました。

VS Codeを使っている場合は、typehintを使うことによって補完が強力になります！

mypy.iniの現在の設定は以下です。

```
[mypy]
warn_redundant_casts = True
warn_unused_ignores = True

# Needed because of bug in MyPy
disallow_subclassing_any = True

mypy_path = stubs
warn_return_any = True
no_implicit_optional = True
strict_optional = True
namespace_packages = True

check_untyped_defs = True
ignore_missing_imports = True
```

VS Codeでmypyを有効にするのを忘れないように。
```
    "python.linting.mypyEnabled": true,
```

オススメの設定があったら教えてください！

## コーディング規約の強制 black

pep8に違反しているかどうかを自身で気にするのは疲れが溜まります。こういうことは自動で解決して欲しい。
ファイルの保存時に自動で修正してくれるblackが便利！

```
$ poetry add black
```
でインストールできます。

VS Codeの設定では、

```
    "python.formatting.provider": "black",
    "editor.formatOnSave": true,
```

で有効になるかと思います。


## おわりに
他に便利な方法があったら教えてください。なるべく手間がかからない方法で良い感じな環境を作りたい。

