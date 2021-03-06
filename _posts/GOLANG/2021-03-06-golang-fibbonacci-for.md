---
layout: post
tags:
- Go
- TIL
categories: TIL
title: Golang Fibbonacci (For)

---
今回はarrayを使わずにfibbonacciのN番目の数字を取り出すプログラムを書いてみた。

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	var input string
	p,q:=0,1 // init
	fmt.Printf("N ? ")
	fmt.Scan(&input)
	i,_ := strconv.Atoi(input) // convert string to int
	n := 0
	for n < i-2 {
		p,q = q, q+p
		n++
	}
	if i < 3 {
		if i%2==0{println(q)}else{println(p)}
	}else {
		println(q)
	}
}
```