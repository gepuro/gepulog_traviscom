---
title: RustでMySQLにデータをインサートした時にエラーが出た時に処理を止めない方法
date: 2022-04-06T20:00:00+09:00
draft: false
tags: [Rust]
---

(Rust 初心者なので、最良の書き方でない可能性が高いです。)

Python などのプログラミング言語で書く場合、トランザクションでエラーが出た場合に try・catch で例外処理に対応することがあります。Rust では、以下のように、match を用いて例外が発生した場合に対応するのが良さそうです。

```{rust}
let res = diesel::insert_into(data)
    .values(new_data)
    .execute(&connection);

match res {
    Ok(_res) => {}
    Err(err) => {
        println!("error {:?}", err);
    }
}
```
