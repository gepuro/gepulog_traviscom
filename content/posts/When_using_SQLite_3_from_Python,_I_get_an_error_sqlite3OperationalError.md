---
title: SQLite3をPythonから使っていたら、sqlite3.OperationalError database or disk is fullというエラーが出た
date: 2016-08-04T16:54:14+09:00
draft: false
tags: [Python3, SQLite3]
aliases:
    - /archives/158
---

タイトルの通りで頭を抱えていました。



Bashで

```

$ df -h

```

を実行してもディスクは溢れていなかったのです。





ググってみると[SQLITE3 VACUUM, “database or disk is full”](http://stackoverflow.com/questions/23249843/sqlite3-vacuum-database-or-disk-is-full)という人がいたので覗いたら解決しました。





```sql

conn = sqlite3.connect("data.db")

cur = self.conn.cursor()

cur.execute("""

    pragma temp_store_directory='/tmp'

    """)

conn.commit()

```



のようにして、一時ディレクトリを指定したら動くようになりました。

デフォルトでは、どこに持ってるのだろうか？そこまでは調べていません。





また、

```

vacuum;

```

というのも覚えました。



SQLite3でデータを追加したり消したりするとゴミが溜まってくるそうで、それを削除出来るとのことです。



データベースが大きくなった時に活躍しそうなので、ついでにメモしておきます。
