---
title: dplyrの暗黒面
date: 2015-02-24T00:47:34+09:00
draft: false
tags: [R言語]
aliases:
    - /archives/120
---

~~~{r}
> library(dplyr)
> iris %>%
+   summarise_(heikin = "mean(Sepal.Length)")
    heikin
1 5.843333
~~~
と実行できるが、

~~~{r}
> myfunc <- function(x){
+   mean(x)
+ }
> iris %>%
+   summarise_(heikin = "myfunc(Sepal.Length)")
Error in summarise_impl(.data, dots) : could not find function "myfunc"
~~~
は実行出来ない。

~~~{r}
> iris %>%
+ summarise_(heikin = as.formula("~myfunc(Sepal.Length)"))
    heikin
1 5.843333
~~~
とすれば、実行できた。

