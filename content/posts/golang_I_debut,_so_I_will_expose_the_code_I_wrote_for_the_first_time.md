---
title: golangデビューしたので、初めて書いたコードを晒す
date: 2014-07-10T02:46:39+09:00
draft: false
tags: [golang]
aliases:
    - /archives/92
---

### フィボナッチ数列を求める
~~~{.code}
package main

import fmt "fmt" 

func fib(n int) int{
	if n <= 2{
		return n
	} else{
		return fib(n-1) + fib(n-2)
	}
}

func main(){
	for i :=1; i < 10; i++{
		fmt.Printf("%d\n", fib(i))
	}
}
~~~

### 素数を求める
~~~{.code}
package main
import fmt "fmt"

func main(){
	// init
	n := 100
	prm := make([]int, n)
	for i := 1; i < n; i++{
		prm[i] = i
	}

	// Sieve_of_Eratosthenes
	for i := 2; i < n; i++{
		if prm[i] == 0{
			continue
		}
		for j := 2; i*j < n; j++{
			prm[i*j] = 0
		}
	}

	// output
	for i := 1; i < n; i++{
		if prm[i] != 0{
			fmt.Printf("%d\n", prm[i])
		}
	}
}
~~~

書きやすくて、流行る理由が分かった気がします。

