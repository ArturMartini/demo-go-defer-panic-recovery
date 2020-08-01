# demo-go-defer-panic-recovery

This project in explanation how to defer, panic and recorery work in GO.

```go
package main

import (
	"fmt"
)

func main() {
	sampleDefers()
	samplePanicRecovery()
}

func sampleDefers() {
	a()
	b()
	cResult := c()
	fmt.Println(cResult)
}

//output of println is 0
func a() {
	i := 0
	defer fmt.Println(i)
	i++
}

//defers functions are put in stack and execute in from last to first
func b() {
	for i := 0; i < 4; i++ {
		defer fmt.Println(i)
	}
}

//this function return 1, but after response, defer receive 1 and increment, return function is 2
//this is sample how to change return using defer
func c() (i int) {
	defer func() {
		fmt.Println(i)
		i++
		fmt.Println(i)
	}()
	return 1
}

func samplePanicRecovery() {
	f()
	fmt.Println("Returned normally from f.")
}

//this program exit with success (return code 0) because function are implementing recovery
//Printing in g 0
//Printing in g 1
//Printing in g 2
//Printing in g 3
//Panicking!
//Defer in g 3
//Defer in g 2
//Defer in g 1
//Defer in g 0
//Recovered in f 4
//Returned normally f
func f() {
	defer func() {
		if r := recover(); r != nil {
			fmt.Println("Recovered in f", r)
		}
	}()
	fmt.Println("Calling g.")
	g(0)
	fmt.Println("Returned normally from g.")
}

func g(i int) {
	if i > 3 {
		fmt.Println("Panicking!")
		panic(fmt.Sprintf("%v", i))
	}
	defer fmt.Println("Defer in g", i)
	fmt.Println("Printing in g", i)
	g(i + 1)
}
```

