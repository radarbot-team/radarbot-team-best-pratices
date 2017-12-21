# Go Best Practices

![Gopher](https://golang.org/doc/gopher/frontpage.png) )

# Index
- [Introduction](#introduction)
- [History and main features](#history-and-main-features)
- [Code Organization](#code-organization)
  * [Overview](#overview)
  * [GOPATH enviroment variable](#gopath-enviroment-variable)
  * [Import Paths](#import-paths)
  * [Package Names](#package-names)
  * [Remote Packages](#remote-packages)
  * [Vendoring](#vendoring)
- [Language Specification](#language-specification)
  * [Formatting](#formatting)    
  * [Comments](#comments)
  * [Name Convention](#name-convention)   
  * [Built-in Types](#built-in-types)
  * [Variables and Constants](#variables-and-constants)   
  * [Data Allocation](#data-allocation)
  * [The Blank Identifier](#the-blank-identifier)
  * [Operators](#operators)    
  * [Control Structures](#control-structures)   
  * [Arrays](#arrays)
  * [Maps](#maps)
  * [Functions](#functions)    
  * [Pointers](#pointers)
  * [Structs](#structs)    
  * [Interfaces](#interfaces)
  * [Embedding](#embedding)
  * [Errors](#errors)
  * [Concurrency](#concurrency)
- [Best Practices Recommendations](#best-practices-recommendations)
  * [Frances Campoy](#frances-campoy)
  * [Peter Bourgon](#peter-bourgon)
- [Common Mistakes](#common-mistakes)

# Introduction

This is a brief introduction to [Go language](https://golang.org/). If you want a more exhaustive guide, we recommend to review [The Golang Language Specification](https://golang.org/ref/spec) & [Effective Go](https://golang.org/doc/effective_go.html), which this document is based on.

If you want a quick review [Tour of Go](https://tour.golang.org/welcome/1), [An introduction to programmig in Go](https://www.golang-book.com/books/intro), [How to Write Go Code](https://golang.org/doc/code.html), [Go in five minutes](https://www.goin5minutes.com/screencasts/), [Go by example](https://gobyexample.com) or [Go wiki](https://github.com/golang/go/wiki) are very good entry points.

You can test quickly the example code provided in this guide via [Go Playgraound](https://play.golang.org/)

Also you can check the [Frequently Asked Questions FAQ](https://golang.org/doc/faq) 


# History and main features

Go was developed as an experiment by Google engineers [Robert Griesemer](https://github.com/griesemer), [Rob Pike](https://en.wikipedia.org/wiki/Rob_Pike) and [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson). It was announced in November 2009.

* It's a **statically typed** language
* It's **compiled to binary code** (no bytecode, bye-bye VM)
* It's **imperative**
* There are **no implicit numeric conversions**
* Source code is **Unicode** text encoded in **UTF-8**
* Has **no classes**, but **structs** with methods.
* An **interface** system in place of virtual inheritance, and **type embedding** instead of non-virtual inheritance
* **Fast compilation** times
* Syntax similar to Java/C/C++, but less parentheses and no semicolons
* **Pointers**, but **no pointer arithmetic**
* A very **fast garbage collector** (depending on heap size < 1ms)
* Functions can **return multiple values**
* **Closures**
* Built-in concurrency primitives: **Goroutines** & **Channels**
* Solves code formatting disagreement with **go fmt**
* Although Go is a general-purpose language was designed with systems programming in mind
* Built-in tools:
   * **go build**, which builds Go binaries using only information in the source files themselves, no separate makefiles
   * **go test**, for unit testing and microbenchmarks
   * **go fmt**, for formatting code
   * **go get**, for retrieving and installing remote packages
   * **go vet**, a static analyzer looking for potential errors in code
   * **go run**, a shortcut for building and executing code
   * **godoc**, for displaying documentation or serving it via HTTP
   * **gorename**, for renaming variables, functions, and so on in a type-safe way
   * **go generate**, a standard way to invoke code generators
* **Mature** : Some of the bests current tools are developed in Go:
   * [Docker](http://www.docker.com/)
   * [Kubernetes](https://github.com/kubernetes/kubernetes)
   * [InfluxDB](https://github.com/influxdb/influxdb)
   * [Consul](https://www.consul.io/)
   * [etcd](https://github.com/coreos/etcd)
   * [minio](https://github.com/minio/minio)
   * [Vegeta](https://github.com/tsenart/vegeta)
   * [traefik](https://github.com/containous/traefik)
   * ...

# Code Organization

## Overview

When working with go you use a **workspace**.

Workspaces allow you to organise your code, including its dependencies (and the automatic pulling of dependencies).

A **workspace** contains many version control repositories and each repository contains one or more **packages** and a package consists of one or more Go source files in a single directory.

The typical layout of a workspace is :

~~~
    bin/                     // contains executable commands
       binary
    pkg/                     // contains package objects
       linux_amd64/
          the/namespace/
                 library.a
    src/                     // contains Go source files
       the/namespace/
          golangfiles.go
       the/other/namespace/
          othergolangfiles.go
~~~          

The go tool builds source packages and installs the resulting binaries to the pkg and bin directories.

## GOPATH enviroment variable

Determines the location of your workspace. A go directory will be mapped by default inside your home directory.

## Import Paths

An import path is a string that uniquely identifies a package. A package's import path corresponds to its location inside a workspace or in a remote repository

## Package Names

* The first statement in a Go source file must be

   ~~~go
   package name
   ~~~
    * Executables are in package main* Convention: package name == last name of import path (import path math/rand => package rand)* Upper case identifier: exported (visible from other packages)* Lower case identifier: private (not visible from other packages)    
    
## Remote Packages

With go tools you can automatically fetch packages from remote repositories

    $ go get github.com/golang/example/hello
    $ $GOPATH/bin/hello
    Hello, Go examples!
    
The **go get** command is able to locate and install the dependent package.

## Vendoring

Although **go get** is a great tool to work with internal dependencies, in big projets with 3rd-party dependencies is necessary a dependency tool to avoid problems like mismatches, conflicts, breaking changes,...

**[Glide](https://github.com/Masterminds/glide)** has been the standard in go dependency tools but the Go Team is working in the official experiment, **[dep](https://github.com/golang/dep)**, that would be the new standard dependency tool. You should keep an eye on it.


# Language Specification 

## Formatting

* Use tabs for indentation (gofmt use them by default).
* Go has no line length limit. If a line feels too long, wrap it and indent with an extra tab.
*  Go needs fewer parentheses than C and Java, an example control structures (if, for, switch) do not have parentheses in their syntax. Also, the operator precedence hierarchy is shorter and clearer, so
   
~~~go
a<<2 + b<<4
~~~

   means what the spacing implies, unlike in the other languages.

## Comments

/* */ C-style  block comment

//  C++-style  line comment (are the norm)

A comment cannot start inside a rune or string literal, or inside a comment.

## Name Convention

* Package name should be short, concise, evocative,lower case and single-word names with no underscores neither mixedCaps. For example the package in src/projections/utm is imported as "projections/utm" but has name utm, not projections_utm neither projetionsUtm.

* One-method interfaces are named by the method name plus an -er suffix: Reader, Writer,...

* The convention in Go is to use MixedCaps or mixedCaps rather than underscores to write multiword names.

* For further information you can review [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments) or Andrew Gerrand's [idiomatic namimg conventions](https://talks.golang.org/2014/names.slide#1).

## Built-in Types

Go has the following built-in types

~~~go
bool
stringint  int8  int16  int32  int64uint uint8 uint16 uint32 uint64 uintptrbyte // alias for uint8rune // alias for int32 float32 float64complex64 complex128
~~~    
    
As you can see is close to C

## Variables and Constants 

Go's declarations read left to right.

The **var** statement declares a list of variables. A big difference from C is that **the type is last**.

The **const** statement declares a list of constants. Constants cannot be declared using the := syntax.

A var statement can be at package or function level. 

~~~go     var hi int                  // no initializationvar hi int = 100            // with initializationvar hi, low int = 100, 0    // declare/init multiple vars at once 
var hi = 100                // type omitted, will be inferred 
hi := 100                   // shorthand, implicit typeconst vatAcronym = "V.A.T"  // constant
~~~

Variables declared without an explicit initial value are given their zero value.

The zero value is:

* 0 for numeric types,
* false for the boolean type, and
* "" (the empty string) for strings    

### Type conversions

~~~go
var i int = 5
var f float64 = float64(i)
var u uint = uint(f)
~~~

## Data Allocation

Go has two allocation instructions : new and make

### new 

**new**, a built-in function to allocate memory. It does not intialize the memory it only zeros it.

new(T) returns a pointer to a newly allocated zero value of type T.

### make

The built-in function **make(T, args)** creates slices, maps, and channels only. It returns an intialized (not zeroed) value of type T (not *T).

## The Blank identifier

_ it represents a write-only value to be used as a placeholder where a variable is needed but the actual value is irrelevant.

For example, you have a function that returns two values, but in some cases you are interestend in only one of this values.

~~~go
var _, value = myFunction(1,2) // returns two variables : error and the result. Only interested in value
~~~


Go doesn't allow missing import neither variables, but when coding a program, unsued imports and variables often arise and it can be annoying to delete them just to have the compilation proceed. You can use the blank identifier to avoid compilation errors.

Sometimes it is used to import a package only for side effects, without an explicit use.

~~~go
import _ "crypto/md5"
~~~

Or to indicate that the declaration exists only for type checking, no to create a variable

~~~go
var _ json.Marshaler = (*RawMessage)(nil)
~~~

## Operators

### Arithmetic

| Operator   | Description |
| --------   | ----------- |
| +          | addition    |
| -          | substraction  |
| *          | multiplication|
| /          | quotient      |
| %          | remainder     |
| &          | bitwise AND   |
| \|         | bitwise OR    |      
| ^          | bitwise XOR   |
| &^         | biclear (AND NOT) |
| <<         | left shift    |
| >>         | right shift (logical) |

### Comparision

|Operator   | Description  |
| --------  | -----------  |
| ==         | equal         |
| !=         | not equal     |
| <          | less than     |
| <=         | less than or equal |
| >          | greater than  |
| >=         | greater than or equal |

### Logical

| Operator | Description   |
| -------- | -----------   |
| &&         | logical AND   |
| \|\|       | logical OR    |      
| !          | NOT           |

### Other  

| Operator   | Description   |
| --------   | -----------   |
| &          | address of / create pointer |
| *          | deference pointer |
| <-         | send / receive operator (Channels) |

## Control Structures

In go control structures reduces to **if**, **switch** and **for**

### If

The expression need not be surrounded by parentheses ( ) but the braces { } are required.

Like for, the if statement can start with a short statement to execute before the condition.

Variables declared by the statement are only in scope until the end of the if

~~~go
if a > b {    return a} else {   return b 
}

// statement before the conditionif a := x + y; a == 100 {    return a} else {    return x + y}

~~~
    
### Switch

Switch cases evaluate cases from top to bottom, stopping when a case succeeds.

~~~go
switch numbers {
   case 0, 2, 4, 6, 8: even()
   case 1, 3, 5, 7, 9: odd()
}

switch x := f(); { 
   case x < 0: return -x
   default: return x
}

switch {
   case x < y: f1()
   case x < z: f2()
}   
~~~
     
#### Type Switches

~~~go
switch t := x.(type) {
    case nil:
    	// type of g is type of x (interface{})
    case string:
    	// type of t is string
     case func(int) float64:
    	// type of t is func(int) float64
    default:
    	// type of t is type of x (interface{})
}
~~~
    
### For
The basic for loop has three components separated by semicolons:

* the init statement: executed before the first iteration
* the condition expression: evaluated before every iteration
* the post statement: executed at the end of every iteration

The init and post statement are optional.

Unlike other languages like C, Java, or Javascript there are no parentheses surrounding the three components of the for statement and the braces { } are always required

~~~go
// There's only for. No while, no untilfor i := 1; i < 100; i++ {}
for ; i < 100;  { // while loop}
for i < 100  { // no semicolons with only condition}
for {         // infinite loop}

// Range For
        
var data [100]string
for i, s := range data {
	// type of i is int, type of s is string
	// s == a[i]
	myFunction(i, s)
}

var key string
var value interface {}  
m := map[string]int{"zero":0, "one":1, "two":2, "three":3, "four":4, "five":5}
for key, value = range m {
	myFunction(key, value)
}
// key == last map key encountered in iteration
// val == map[key]

// Channels
var ch chan Work = producer()
for w := range ch {
	doWork(w)
}

// empty a channel
for range ch {}
~~~
        
## Arrays

The type [n]T is an array of n values of type T.

There are major differences between the ways arrays work in Go and C. In Go,

* Arrays are values. Assigning one array to another copies all the elements.
* In particular, if you pass an array to a function, it will receive a copy of the array, not a pointer to it.
* The size of an array is part of its type. The types [10]int and [20]int are distinct.

Some array declarations and initializatinos examples: 

~~~go
var arr[100]int    // int array 100 elementsarr[50] = 1        // set elementx := arr[50]       // read element
// declaration & initializationvar arr = [2]int{10, 20}arr := [2]int{10, 20}    //shorthandarr := [...]int{10, 20}  // Compiler resolves array length
~~~
## Slices

An array has a fixed size. A slice, on the other hand, is a dynamically-sized, flexible view into the elements of an array. In practice, slices are much more common than arrays.

The type []T is a slice with elements of type T.

Slices holds references to an underlaying array. A slice does not store any data. Changing the elements of a slice modifies the corresponding elements of its underlying array.

A slice has both a length and a capacity.

The length of a slice is the number of elements it contains.

The capacity of a slice is the number of elements in the underlying array, counting from the first element in the slice.

The length and capacity of a slice s can be obtained using the expressions len(s) and cap(s).

The zero value of a slice is nil. A nil slice has a length and capacity of 0 and has no underlying array.

Slices can be created with the built-in make function; this is how you create dynamically-sized arrays.

The make function allocates a zeroed array and returns a slice that refers to that array.

~~~go
var arr []int                          // slice is like an array but with no length 
var arr = []int {10, 20, 30, 40}       // declare and initialize a slice
                                      
arr := []int{10, 20, 30, 40}           // shorthand
 
var s = arr[1:4]    // slice from index 1 to 3
var s = arr[:3]     // missing low index implies 0
var s = arr[3:]     // missing high index implies len(arr)
  
// creating slice with make
arr= make([]byte, 100, 10)  // first arg length, second capacity (is optional)

// len(arr) told you the length of an array/a slice. It's a built-in function

// loop over an array/slice
for i, e := range arr {
    // i is the index, e the element
}

~~~
   
## Maps

A map maps keys to values.

The zero value of a map is nil. A nil map has no keys, nor can keys be added.

The make function returns a map of the given type, initialized and ready for use.

~~~go
var myMap map[string]int

myMap = make(map[string]int)

myMap["key"] = 100

var data= myMap["key"]

delete(myMap, "key")

elem, ok := myMap["key"] // obtaining "key" from myMap into elem

~~~

## Functions

Functions are values too. They can be passed around just like other values.

Function values may be used as function arguments and return values.

A function declaration has a name, a list of parameters, an optional list of results, and a body :~~~gofunc name(parameter-list) (result-list) {
   body}
~~~

### Examples of function declarations

~~~go
// Easiest
func myFunction() {}

// function with parameters
func myFunction(param1 int, param2 string) {}

// parameters of the same type
func functionName(param1, param2 int) {}

// return type declaration
func myFunction() string {
    return "Hello World!"
}

// return multiple values 
func myFunction() (string, int) {
    return "Hello World!", 2017
}
var msg, year = myFunction()

~~~
    
### Functions As Values And Closures

A closure is a function value that references variables from outside its body. The function may access and assign to the referenced variables; in this sense the function is "bound" to the variables.~~~go

func outer() (func() int, int) {
    outerVar := 10       // outerVar is outside inner's scope
    inner := func() int {
        outerVar += 100  
        return outerVar // 110 but outerVar is a newly redefined variable 
                         // visible only inside inner
    }                     
    return inner, outerVar // => 110, 10 
     
} 
~~~
 
### Variadic Functions
 
~~~go 
func acum(args ...int) int {
    tmp := 0
    for _, v := range args {
        tmp += v
    }
    return tmp
} 

func main() {
    fmt.Println(acum(10, 20, 30)) // 60
    data := []int{100, 200, 300}
    fmt.Println(acum(data...)) // 600
}
  
~~~
    
### Defer

Go's defer statement schedules a function call (the deferred function) to be run immediately before the function executing the defer returns.    

~~~go
// Contents returns the file's contents as a string.
func Contents(filename string) (string, error) {
    f, err := os.Open(filename)
    if err != nil {
        return "", err
    }
    defer f.Close()  // f.Close will run when we're finished.

    var result []byte
    buf := make([]byte, 100)
    for {
        n, err := f.Read(buf[0:])
        result = append(result, buf[0:n]...) // append is discussed later.
        if err != nil {
            if err == io.EOF {
                break
            }
            return "", err  // f will be closed if we return here.
        }
    }
    return string(result), nil // f will be closed if we return here.
}
~~~

## Pointers

A variable is a piece of storage containing a value. A pointer value is the address of a variable. 

A pointer is thus the location at which a value is stored. With a pointer, we can read or update the value of a variable indirectly, without using or even knowing the name of the variable.

The type *T is a pointer to a T value.

The zero value for a pointer of any type is nil.

Another way to create a variable is to use t he built-in function new. The expression new(T) creates an unnamed variable of type T, initializes it to the zero value of T, and returns its address, which is a value of type *T.

~~~go
p := myStruc{1, 2}   // p is a structure ot type myStructq := &p              // q is a pointer to myStructvar tmp *myStruct = new(myStruct) // tmp is a pointer to myStruct
~~~

## Structs
A struct is an aggregate data type that groups together zero or more named values of arbitrary types as a single entity.

~~~go
// A struct is a type. It's also a collection of fields
// Declarationtype mySttruc struct {    A, B int}
// Creatingvar s = myStruc{1, 2}var s = myStruc{A: 1, B: 2} fmt.Println( s.A )// You can declare methods on structs. 
// The struct is copied on each method callfunc (s myStruct) Add() int {    return s.A + s.B}s.Add() // Call method// If you use a pointer the struct value is not copied for each method call
// The struct values will be modifiedfunc (s *myStruc) Acum(tmp int) {    s.A += tmp    s.B += tmp 
}
~~~

### Anonymous Structs

~~~godata := struct {    A, B int}{1, 2}
~~~

## Interfaces

Interface types express generalizations or abstractions about the behaviors of other types.

A type can implement multiple interfaces.

~~~go
// interface declarationtype myInterface interface {    helloMsg() string}
// Types implicitly satisfy an interface if they implement all required methods
// Type Foo implements the interface myInterfacefunc (foo Foo) helloMsg() string {   return "Hello World!" 
}
~~~

The interface type that specifies zero methods is known as the empty interface:

~~~go
interface{}
~~~

An empty interface may hold values of any type. (Every type implements at least zero methods.)

Empty interfaces are used by code that handles values of unknown type. For example, fmt.Print takes any number of arguments of type interface{}.

## Embedding

There is no inheritance in Go. Composition is the way to work with go.

In C++ you can inherit from another class

~~~go
class A : public B
~~~

In go you can do it in another way, composition:

~~~go
package main

import "fmt"

type foo interface {
	msg() string
}

type A struct  {
}

func (a A) msg() string {
	return "Hello World!"
}

type B struct {
	A
}

func main() {
	var b = B{}
	fmt.Println(b.msg())
}

~~~

## Errors

In go there is no exception handling like in other languages like Java.

Go provides the Error interface:

~~~gotype error interface {    Error() string}
~~~

So a function that might produce an error should follow the following example:~~~gofunc myFunction() (int, error) {}
func main() {    result, error := myFunction()    if (error != nil) {        // manage the error    } else {        // do what you want    }
}
~~~

## Concurrency

### Goroutines

A goroutine is a function that is capable of running concurrently with other functions in the same address space. It's like a lightweight thread managed by the Go runtime.

A **go** statement causes the function to be called in a newly created goroutine. The go statement itself completes immediately:

~~~gof()    // call f(); wait for it to returngo f() // create a new goroutine that calls f(); don't wait
~~~

We can invoke our functions as goroutines easily:

~~~gofunc myFunction(s string) {}
func main() {    // using a named function in a goroutine
    go myFunction("Hello World!")
        // using an anonymous inner function in a goroutine    go func (x int) {        // function body goes here    }(2017)
}
~~~

### Channels

Channels are a typed conduit through which you can send and receive values with the channel operator, <-

Like maps and slices, channels must be created before use with make built-in function.

~~~go
ch := make(chan int) // create a channel of type intch <- 2017           // Send a value to the channel chv := <-ch            // Receive a value from ch
close(ch) // closes the channel (only sender should close)// Read from channel and test if it has been closed// If ok is false, channel has been closedv, ok := <-ch// Read from channel until it is closedfor i := range ch {    fmt.Println(i)}~~~

The select statement lets a goroutine wait on multiple communication operations.

A select blocks until one of its cases can run, then it executes that case. It chooses one at random if multiple are ready.

~~~gofunc myFunction(chanOut, chanIn chan int) {    select {    case chanOut <- 2017:        fmt.Println("Writing!") 
    case x := <- chanIn:        fmt.Println("Reading") 
    case <-time.After(time.Second * 1):        fmt.Println("1 second timeout") }}
~~~

#### Channel Axioms

~~~go
// I. A send to a nil channel blocks forevervar c chan stringc <- "Hello, World!"// fatal error: all goroutines are asleep - deadlock!
// II. A receive from a nil channel blocks forevervar c chan stringfmt.Println(<-c)// fatal error: all goroutines are asleep - deadlock!// III. A send to a closed channel panicsvar c = make(chan string, 1) 
c <- "Hello, World!" 
close(c)
c <- "Hello, Panic!"// panic: send on closed channel       
// IV. A receive from a close channel returns the zero value immediatelyvar c = make(chan int, 2)c <- 1c <- 2close(c)for i := 0; i < 3; i++ { 
    fmt.Printf("%d ", <-c)}// 1 2 0
~~~

### Mutex

We've seen how channels are great for communication among goroutines.

But what if we don't need communication? What if we just want to make sure only one goroutine can access a variable at a time to avoid conflicts?

This concept is called mutual exclusion, and the conventional name for the data structure that provides it is mutex.

Go's standard library provides mutual exclusion with sync.Mutex and its two methods:

* Lock
* Unlock

We can define a block of code to be executed in mutual exclusion by surrounding it with a call to Lock and Unlock as shown on the Inc method.

We can also use defer to ensure the mutex will be unlocked as in the Value method.

~~~go
package main

import (
	"fmt"
	"sync"
	"time"
)

// SafeCounter is safe to use concurrently.
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc increments the counter for the given key.
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	c.v[key]++
	c.mux.Unlock()
}

// Value returns the current value of the counter for the given key.
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock so only one goroutine at a time can access the map c.v.
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}
~~~

### Recommended Concurrency Talks

* [Concurrency is not parallelism] (https://vimeo.com/49718712)
* [Go concurrency patterns] (http://www.youtube.com/watch?v=f6kdp27TYZs)
* [Advanced Go concurrency patterns] (http://www.youtube.com/watch?v=QDDwwePbDtw)


# Best Practices Recommendations

The following best practices recommendations were obtained from different blogs and youtube videos. These best practices are copied here for a simple a direct reading in this guide, but the link to the original document (blog or video) is provided so you can read/watch directly from this author.

## Frances Campoy

These Best Practices were explained in Frances's talk [Twelve Go Best Practices](https://talks.golang.org/2013/bestpractices.slide#1)
 
### Avoid nesting by handling errors first

Some code

~~~go

type Gopher struct {
        Name     string
        AgeYears int
}

// Example of bad code, missing early return. OMIT
func (g *Gopher) WriteTo(w io.Writer) (size int64, err error) {
        err = binary.Write(w, binary.LittleEndian, int32(len(g.Name)))
        if err == nil {
                size += 4
                var n int
                n, err = w.Write([]byte(g.Name))
                size += int64(n)
                if err == nil {
                        err = binary.Write(w, binary.LittleEndian, int64(g.AgeYears))
                        if err == nil {
                                size += 4
                        }
                        return
                }
                return
        }
        return
}
~~~

Should be

~~~go
func (g *Gopher) WriteTo(w io.Writer) (size int64, err error) {
    err = binary.Write(w, binary.LittleEndian, int32(len(g.Name)))
    if err != nil {
        return
    }
    size += 4
    n, err := w.Write([]byte(g.Name))
    size += int64(n)
    if err != nil {
        return
    }
    err = binary.Write(w, binary.LittleEndian, int64(g.AgeYears))
    if err == nil {
        size += 4
    }
    return
}
~~~
Less nesting means less cognitive load on the reader

### Important code goes first 

~~~go
import (
    "fmt"
    "io"
    "log"

    "golang.org/x/net/websocket"
)
~~~

### Document your code 

Package name, with the associated documentation before.

~~~go
// Package playground registers an HTTP handler at "/compile" that
// proxies requests to the golang.org playground service.
package playground
~~~

Exported identifiers appear in godoc, they should be documented correctly.

~~~go
/ Author represents the person who wrote and/or is presenting the document.
type Author struct {
    Elem []Elem
}

// TextElem returns the first text elements of the author details.
// This is used to display the author' name, job title, and company
// without the contact details.
func (p *Author) TextElem() (elems []Elem) {
~~~

### Shorter is better 

Or at least longer is not always better.

Try to find the shortest name that is self explanatory.

* Prefer MarshalIndent to MarshalWithIndentation.

Don't forget that the package name will appear before the identifier you chose.

* In package encoding/json we find the type Encoder, not JSONEncoder.
* It is referred as json.Encoder.
* 
### Packages with multiple files 

Should you split a package into multiple files?

* Avoid very long files

The net/http package from the standard library contains 15734 lines in 47 files.

* Separate code and tests

   net/http/cookie.go and net/http/cookie_test.go are both part of the http 
package.
Test code is compiled only at test time.

* Separated package documentation

When we have more than one file in a package, it's convention to create a doc.go 
containing the package documentation.

### Make your packages "go get"-able 

Some packages are potentially reusable, some others are not.

A package defining some network protocol might be reused while one defining 
an executable command may not.

### Ask for what you need 

Let's use the Gopher type from before

~~~go
type Gopher struct {
    Name     string
    AgeYears int
}
~~~

We could define this method

~~~go
func (g *Gopher) WriteToFile(f *os.File) (int64, error) {
~~~

But using a concrete type makes this code difficult to test, so we use an interface.

~~~go
func (g *Gopher) WriteToReadWriter(rw io.ReadWriter) (int64, error) {
~~~

And, since we're using an interface, we should ask only for the methods we need.

~~~go
func (g *Gopher) WriteToWriter(f io.Writer) (int64, error) {
~~~

### Keep independent packages independent 
~~~go
import (
    "golang.org/x/talks/2013/bestpractices/funcdraw/drawer"
    "golang.org/x/talks/2013/bestpractices/funcdraw/parser"
)
~~~

~~~go
// Parse the text into an executable function.
f, err := parser.Parse(text)
if err != nil {
    log.Fatalf("parse %q: %v", text, err)
}

// Create an image plotting the function.
m := drawer.Draw(f, *width, *height, *xmin, *xmax)

// Encode the image into the standard output.
err = png.Encode(os.Stdout, m)
if err != nil {
    log.Fatalf("encode image: %v", err)
}
~~~

#### Parsing

~~~go
type ParsedFunc struct {
    text string
    eval func(float64) float64
}

func Parse(text string) (*ParsedFunc, error) {
    f, err := parse(text)
    if err != nil {
        return nil, err
    }
    return &ParsedFunc{text: text, eval: f}, nil
}

func (f *ParsedFunc) Eval(x float64) float64 { return f.eval(x) }
func (f *ParsedFunc) String() string         { return f.text }
~~~

#### Drawing

~~~go
import (
    "image"

    "golang.org/x/talks/2013/bestpractices/funcdraw/parser"
)

// Draw draws an image showing a rendering of the passed ParsedFunc.
func DrawParsedFunc(f parser.ParsedFunc) image.Image {
~~~

Avoid dependency by using an interface.

~~~go
import "image"

// Function represent a drawable mathematical function.
type Function interface {
    Eval(float64) float64
}

// Draw draws an image showing a rendering of the passed Function.
func Draw(f Function) image.Image {
~~~

### Testing

Using an interface instead of a concrete type makes testing easier.

~~~go
package drawer

import (
    "math"
    "testing"
)

type TestFunc func(float64) float64

func (f TestFunc) Eval(x float64) float64 { return f(x) }

var (
    ident = TestFunc(func(x float64) float64 { return x })
    sin   = TestFunc(math.Sin)
)

func TestDraw_Ident(t *testing.T) {
    m := Draw(ident)
    // Verify obtained image.
~~~

### Avoid concurrency in your API 

~~~go
func doConcurrently(job string, err chan error) {
    go func() {
        fmt.Println("doing job", job)
        time.Sleep(1 * time.Second)
        err <- errors.New("something went wrong!")
    }()
}

func main() {
    jobs := []string{"one", "two", "three"}

    errc := make(chan error)
    for _, job := range jobs {
        doConcurrently(job, errc)
    }
    for _ = range jobs {
        if err := <-errc; err != nil {
            fmt.Println(err)
        }
    }
}
~~~
What if we want to use it sequentially?

~~~go
func do(job string) error {
    fmt.Println("doing job", job)
    time.Sleep(1 * time.Second)
    return errors.New("something went wrong!")
}

func main() {
    jobs := []string{"one", "two", "three"}

    errc := make(chan error)
    for _, job := range jobs {
        go func(job string) {
            errc <- do(job)
        }(job)
    }
    for _ = range jobs {
        if err := <-errc; err != nil {
            fmt.Println(err)
        }
    }
}
~~~
Expose synchronous APIs, calling them concurrently is easy.

### Use goroutines to manage state 
Use a chan or a struct with a chan to communicate with a goroutine

~~~go
type Server struct{ quit chan bool }

func NewServer() *Server {
    s := &Server{make(chan bool)}
    go s.run()
    return s
}

func (s *Server) run() {
    for {
        select {
        case <-s.quit:
            fmt.Println("finishing task")
            time.Sleep(time.Second)
            fmt.Println("task done")
            s.quit <- true
            return
        case <-time.After(time.Second):
            fmt.Println("running task")
        }
    }
}

func (s *Server) Stop() {
    fmt.Println("server stopping")
    s.quit <- true
    <-s.quit
    fmt.Println("server stopped")
}

func main() {
    s := NewServer()
    time.Sleep(2 * time.Second)
    s.Stop()
}
~~~

## Peter Bourgon 

These Best Practices were obtained from [Go best practices, six years in](https://peter.bourgon.org/go-best-practices-2016/)

1. Put $GOPATH/bin in your $PATH, so installed binaries are easily accessible.
2. Put library code under a pkg/ subdirectory. Put binaries under a cmd/ subdirectory.
3. Always use fully-qualified import paths. Never use relative imports.
4. Defer to Andrew Gerrand’s [naming conventions](https://talks.golang.org/2014/names.slide).
5. Only func main has the right to decide which flags are available to the user.
6. Use struct literal initialization to avoid invalid intermediate state.
7. Avoid nil checks via default no-op implementations.
8. Make the zero value useful, especially in config objects.
9. Make dependencies explicit! 
10. Loggers are dependencies, just like references to other components, database handles, commandline flags, etc.
11. Use many small interfaces to model dependencies.
12. Tests only need to test the thing being tested.
13. Use a top tool to vendor dependencies for your binary.
14. Libraries should never vendor their dependencies.
15. Prefer go install to go build. 

# Common Mistakes

This common mistakes were obtained from [50 Shades of Go: Traps, Gotchas, and Common Mistakes for New Golang Devs](http://devs.cloudimmunity.com/gotchas-and-common-mistakes-in-go-golang)

## Begginer Level

### Opening Brace Can't Be Placed on a Separate Line

**Wrong**

~~~go
package main

import "fmt"

func main()  
{ //error, can't have the opening brace on a separate line
    fmt.Println("hello there!")
}
~~~

**OK**

~~~go
package main

import "fmt"

func main() {  
    fmt.Println("works!")
}
~~~

### Unused Variables

**Wrong**

~~~go
package main

var gvar int //not an error

func main() {  
    var one int   //error, unused variable
    two := 2      //error, unused variable
    var three int //error, even though it's assigned 3 on the next line
    three = 3

    func(unused string) {
        fmt.Println("Unused arg. No compile error")
    }("what?")
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    var one int
    _ = one

    two := 2 
    fmt.Println(two)

    var three int 
    three = 3
    one = three

    var four int
    four = four
}
~~~

### Unused Imports

**Wrong**

~~~go
import (  
    "fmt"
    "log"
    "time"
)

func main() {  
}
~~~

**OK** 

~~~go
package main

import (  
    _ "fmt"
    "log"
    "time"
)

var _ = log.Println

func main() {  
    _ = time.Now
}
~~~

### Short Variable Declarations Can Be Used Only Inside Functions

**Wrong**

~~~go
package main

myvar := 1 //error

func main() {  
}
~~~

**OK** 

~~~go
package main

var myvar = 1

func main() {  
}
~~~

### Redeclaring Variables Using Short Variable Declarations

**Wrong**

~~~go
package main

func main() {  
    one := 0
    one := 1 //error
}
~~~

**OK** 

~~~go
package main

func main() {  
    one := 0
    one, two := 1,2

    one,two = two,one
}
~~~

### Can't Use Short Variable Declarations to Set Field Values

**Wrong**

~~~go
ackage main

import (  
  "fmt"
)

type info struct {  
  result int
}

func work() (int,error) {  
    return 13,nil  
  }

func main() {  
  var data info

  data.result, err := work() //error
  fmt.Printf("info: %+v\n",data)
}
~~~

**OK** 

~~~go
package main

import (  
  "fmt"
)

type info struct {  
  result int
}

func work() (int,error) {  
    return 13,nil  
  }

func main() {  
  var data info

  var err error
  data.result, err = work() //ok
  if err != nil {
    fmt.Println(err)
    return
  }

  fmt.Printf("info: %+v\n",data) //prints: info: {result:13}
}
~~~

### Accidental Variable Shadowing

~~~go
package main

import "fmt"

func main() {  
    x := 1
    fmt.Println(x)     //prints 1
    {
        fmt.Println(x) //prints 1
        x := 2
        fmt.Println(x) //prints 2
    }
    fmt.Println(x)     //prints 1 (bad if you need 2)
}
~~~

### Can't Use "nil" to Initialize a Variable Without an Explicit Type

**Wrong**

~~~go
package main

func main() {  
    var x = nil //error

    _ = x
}
~~~

**OK** 

~~~go
package main

func main() {  
    var x interface{} = nil

    _ = x
}
~~~

### Using "nil" Slices and Maps

It's OK to add items to a "nil" slice, but doing the same with a map will produce a runtime panic.
**Wrong**

~~~go
package main

func main() {  
    var m map[string]int
    m["one"] = 1 //error

}
~~~

**OK** 

~~~go
package main

func main() {  
    var s []int
    s = append(s,1)
}
~~~

### Map Capacity

You can specify the map capacity when it's created, but you can't use the cap() function on maps.
**Wrong**

~~~go
package main

func main() {  
    m := make(map[string]int,99)
    cap(m) //error
}
~~~

### Strings Can't Be "nil"

**Wrong**

~~~go
package main

func main() {  
    var x string = nil //error

    if x == nil { //error
        x = "default"
    }
}
~~~

**OK** 

~~~go
package main

func main() {  
    var x string //defaults to "" (zero value)

    if x == "" {
        x = "default"
    }
}
~~~

### Array Function Arguments

**Wrong** (if you want to modify the array)

~~~go
package main

import "fmt"

func main() {  
    x := [3]int{1,2,3}

    func(arr [3]int) {
        arr[0] = 7
        fmt.Println(arr) //prints [7 2 3]
    }(x)

    fmt.Println(x) //prints [1 2 3] (not ok if you need [7 2 3])
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    x := [3]int{1,2,3}

    func(arr *[3]int) {
        (*arr)[0] = 7
        fmt.Println(arr) //prints &[7 2 3]
    }(&x)

    fmt.Println(x) //prints [7 2 3]
}
~~~

### Unexpected Values in Slice and Array "range" Clauses

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    x := []string{"a","b","c"}

    for v := range x {
        fmt.Println(v) //prints 0, 1, 2
    }
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    x := []string{"a","b","c"}

    for _, v := range x {
        fmt.Println(v) //prints a, b, c
    }
}
~~~

### Accessing Non-Existing Map Keys

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    x := map[string]string{"one":"a","two":"","three":"c"}

    if v := x["two"]; v == "" { //incorrect
        fmt.Println("no entry")
    }
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    x := map[string]string{"one":"a","two":"","three":"c"}

    if _,ok := x["two"]; !ok {
        fmt.Println("no entry")
    }
}
~~~

### Strings Are Immutable

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    x := "text"
    x[0] = 'T'

    fmt.Println(x)
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    x := "text"
    xbytes := []byte(x)
    xbytes[0] = 'T'

    fmt.Println(string(xbytes)) //prints Text
}
~~~

### Strings Are Not Always UTF8 Text

~~~go
package main

import (  
    "fmt"
    "unicode/utf8"
)

func main() {  
    data1 := "ABC"
    fmt.Println(utf8.ValidString(data1)) //prints: true

    data2 := "A\xfeC"
    fmt.Println(utf8.ValidString(data2)) //prints: false
}
~~~

### String Length

~~~go
package main

import (
	"fmt"
	"unicode/utf8"
)

func main() {
	data := "♥"
	fmt.Println(len(data)) //prints: 3
	fmt.Println(utf8.RuneCountInString(data)) //prints: 1

	other := "é"
	fmt.Println(len(other))                    //prints: 3
	fmt.Println(utf8.RuneCountInString(other)) //prints: 2
}
~~~

### Missing Comma In Multi-Line Slice, Array, and Map Literals

**Wrong**

~~~go
package main

func main() {  
    x := []int{
    1,
    2 //error
    }
    _ = x
}
~~~

**OK** 

~~~go
package main

func main() {
	x := []int{
		1,
		2,
	}
	x = x

	y := []int{3,4,} //no error
	y = y
}

~~~

### log.Fatal and log.Panic Do More Than Log

Logging libraries often provide different log levels. Unlike those logging libraries, the log package in Go does more than log if you call its Fatal*() and Panic*() functions. When your app calls those functions Go will also terminate your app

### Built-in Data Structure Operations Are Not Synchronized

Even though Go has a number of features to support concurrency natively, concurrency safe data collections are not one them :-) It's your responsibility to ensure the data collection updates are atomic. Goroutines and channels are the recommended way to implement those atomic operations, but you can also leverage the "sync" package if it makes sense for your application.


### Iteration Values For Strings in "range" Clauses

~~~go
package main

import "fmt"

func main() {
	data := "A\xfe\x02\xff\x04"
	for _, v := range data {
		fmt.Printf("%#x ", v)
	}
	//prints: 0x41 0xfffd 0x2 0xfffd 0x4 (not ok)

	fmt.Println()
	for _, v := range []byte(data) {
		fmt.Printf("%#x ", v)
	}
	//prints: 0x41 0xfe 0x2 0xff 0x4 (good)
}

~~~

### Fallthrough Behavior in "switch" Statements

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    isSpace := func(ch byte) bool {
        switch(ch) {
        case ' ': //error
        case '\t':
            return true
        }
        return false
    }

    fmt.Println(isSpace('\t')) //prints true (ok)
    fmt.Println(isSpace(' '))  //prints false (not ok)
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    isSpace := func(ch byte) bool {
        switch(ch) {
        case ' ', '\t':
            return true
        }
        return false
    }

    fmt.Println(isSpace('\t')) //prints true (ok)
    fmt.Println(isSpace(' '))  //prints true (ok)
}
~~~

### Increment and Decrement

**Wrong**

~~~go
package main

import "fmt"

func main() {
	data := []int{1,2,3}
	i := 0
	++i //error
	fmt.Println(data[i++]) //error
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    data := []int{1,2,3}
    i := 0
    i++
    fmt.Println(data[i])
}
~~~

### Bitwise NOT Operator

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    fmt.Println(~2) //error
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    var d uint8 = 2
    fmt.Printf("%08b\n",^d)
}
~~~

### Unexported Structure Fields Are Not Encoded

The struct fields starting with lowercase letters will not be (json, xml, gob, etc.) encoded, so when you decode the structure you'll end up with zero values in those unexported fields.

~~~go
package main

import (  
    "fmt"
    "encoding/json"
)

type MyData struct {  
    One int
    two string
}

func main() {  
    in := MyData{1,"two"}
    fmt.Printf("%#v\n",in) //prints main.MyData{One:1, two:"two"}

    encoded,_ := json.Marshal(in)
    fmt.Println(string(encoded)) //prints {"One":1}

    var out MyData
    json.Unmarshal(encoded,&out)

    fmt.Printf("%#v\n",out) //prints main.MyData{One:1, two:""}
}
~~~

### App Exits With Active Goroutines

**Wrong**

~~~go
package main

import (
	"fmt"
	"time"
)

func main() {
	workerCount := 2

	for i := 0; i < workerCount; i++ {
		go doit(i)
	}
	time.Sleep(1 * time.Second)
	fmt.Println("all done!")
}

func doit(workerId int) {
	fmt.Printf("[%v] is running\n",workerId)
	time.Sleep(3 * time.Second)
	fmt.Printf("[%v] is done\n",workerId)
}

/*
 You will see

 [1] is running
 [0] is running
  all done!
*/
~~~

**OK** A solution implementing WaitingGroups

~~~go
package main

import (  
    "fmt"
    "sync"
)

func main() {  
    var wg sync.WaitGroup
    done := make(chan struct{})
    wq := make(chan interface{})
    workerCount := 2

    for i := 0; i < workerCount; i++ {
        wg.Add(1)
        go doit(i,wq,done,&wg)
    }

    for i := 0; i < workerCount; i++ {
        wq <- i
    }

    close(done)
    wg.Wait()
    fmt.Println("all done!")
}

func doit(workerId int, wq <-chan interface{},done <-chan struct{},wg *sync.WaitGroup) {  
    fmt.Printf("[%v] is running\n",workerId)
    defer wg.Done()
    for {
        select {
        case m := <- wq:
            fmt.Printf("[%v] m => %v\n",workerId,m)
        case <- done:
            fmt.Printf("[%v] is done\n",workerId)
            return
        }
    }
}
~~~

### Sending to an Unbuffered Channel Returns As Soon As the Target Receiver Is Ready

The sender will not be blocked until your message is processed by the receiver.

~~~go
package main

import "fmt"

func main() {  
    ch := make(chan string)

    go func() {
        for m := range ch {
            fmt.Println("processed:",m)
        }
    }()

    ch <- "cmd.1"
    ch <- "cmd.2" //won't be processed
}
~~~

### Sending to an Closed Channel Causes a Panic

**Wrong**

~~~go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    ch := make(chan int)
    for i := 0; i < 3; i++ {
        go func(idx int) {
            ch <- (idx + 1) * 2
        }(i)
    }

    //get the first result
    fmt.Println(<-ch)
    close(ch) //not ok (you still have other senders)
    //do other work
    time.Sleep(2 * time.Second)
}
~~~

**OK** 

~~~go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    ch := make(chan int)
    done := make(chan struct{})
    for i := 0; i < 3; i++ {
        go func(idx int) {
            select {
            case ch <- (idx + 1) * 2: fmt.Println(idx,"sent result")
            case <- done: fmt.Println(idx,"exiting")
            }
        }(i)
    }

    //get first result
    fmt.Println("result:",<-ch)
    close(done)
    //do other work
    time.Sleep(3 * time.Second)
}
~~~

### Using "nil" Channels

Send and receive operations on a nil channel block forver

**Wrong**

~~~go
package main

import (
"fmt"
"time"
)

func main() {
var ch chan int
for i := 0; i < 3; i++ {
go func(idx int) {
ch <- (idx + 1) * 2
}(i)
}

//get first result
fmt.Println("result:",<-ch)
//do other work
time.Sleep(2 * time.Second)
}


//  fatal error: all goroutines are asleep - deadlock!~~~

**OK** 

~~~go
package main

import "fmt"
import "time"

func main() {
	inch := make(chan int)
	outch := make(chan int)

	go func() {
		var in <- chan int = inch
		var out chan <- int
		var val int
		for {
			select {
			case out <- val:
				out = nil
				in = inch
			case val = <- in:
				out = outch
				in = nil
			}
		}
	}()

	go func() {
		for r := range outch {
			fmt.Println("result:",r)
		}
	}()

	time.Sleep(0)
	inch <- 1
	inch <- 2
	time.Sleep(3 * time.Second)
}
~~~

### Methods with Value Receivers Can't Change the Original Value

~~~go
package main

import "fmt"

type data struct {
	num int
	key *string
	items map[string]bool
}

func (this *data) pmethod() {
	this.num = 7
}

func (this data) vmethod() {
	this.num = 8
	*this.key = "v.key"
	this.items["vmethod"] = true
}

func main() {
	key := "key.1"
	d := data{1,&key,make(map[string]bool)}

	fmt.Printf("num=%v key=%v items=%v\n",d.num,*d.key,d.items)
	//prints num=1 key=key.1 items=map[]

	d.pmethod()
	fmt.Printf("num=%v key=%v items=%v\n",d.num,*d.key,d.items)
	//prints num=7 key=key.1 items=map[]

	d.vmethod()
	fmt.Printf("num=%v key=%v items=%v\n",d.num,*d.key,d.items)
	//prints num=7 key=v.key items=map[vmethod:true]
}
~~~

## Intermediate Level

### Closing HTTP Response Body

**Wrong**

~~~go

/*
  This code works for successful requests, but if the http request fails the resp variablemight be nil, 
  which will cause a runtime panic.
*/
package main

import (  
    "fmt"
    "net/http"
    "io/ioutil"
)

func main() {  
    resp, err := http.Get("https://api.ipify.org?format=json")
    defer resp.Body.Close()//not ok
    if err != nil {
        fmt.Println(err)
        return
    }

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Println(string(body))
}

~~~

**OK** 

~~~go
package main

import (  
    "fmt"
    "net/http"
    "io/ioutil"
)

func main() {  
    resp, err := http.Get("https://api.ipify.org?format=json")
    if resp != nil {
        defer resp.Body.Close()
    }

    if err != nil {
        fmt.Println(err)
        return
    }

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Println(string(body))
}
~~~

### Closing HTTP Connections

**Wrong**

~~~go
package main

import (  
    "fmt"
    "net/http"
    "io/ioutil"
)

func main() {  
    req, err := http.NewRequest("GET","http://golang.org",nil)
    if err != nil {
        fmt.Println(err)
        return
    }

    req.Close = true
    //or do this:
    //req.Header.Add("Connection", "close")

    resp, err := http.DefaultClient.Do(req)
    if resp != nil {
        defer resp.Body.Close()
    }

    if err != nil {
        fmt.Println(err)
        return
    }

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Println(len(string(body)))
}
~~~

**OK** 

~~~go
package main

import (  
    "fmt"
    "net/http"
    "io/ioutil"
)

func main() {  
    tr := &http.Transport{DisableKeepAlives: true}
    client := &http.Client{Transport: tr}

    resp, err := client.Get("http://golang.org")
    if resp != nil {
        defer resp.Body.Close()
    }

    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Println(resp.StatusCode)

    body, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        fmt.Println(err)
        return
    }

    fmt.Println(len(string(body)))
}
~~~

### Recovering From a Panic

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    recover() //doesn't do anything
    panic("not good")
    recover() //won't be executed :)
    fmt.Println("ok")
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    defer func() {
        fmt.Println("recovered:",recover())
    }()

    panic("not good")
}

~~~

**Fails**

~~~go
package main

import "fmt"

func doRecover() {
	fmt.Println("recovered =>",recover()) //prints: recovered => <nil>
}

func main() {
	defer func() {
		doRecover() //panic is not recovered
	}()

	panic("not good")
}
~~~

### Updating and Referencing Item Values in Slice, Array, and Map "range" Clauses

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    data := []int{1,2,3}
    for _,v := range data {
        v *= 10 //original item is not changed
    }

    fmt.Println("data:",data) //prints data: [1 2 3]
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    data := []int{1,2,3}
    for i,_ := range data {
        data[i] *= 10
    }

    fmt.Println("data:",data) //prints data: [10 20 30]
}
~~~

**OK Alternative**

~~~go
package main

import "fmt"

func main() {  
    data := []*struct{num int} {{1},{2},{3}}

    for _,v := range data {
        v.num *= 10
    }

    fmt.Println(data[0],data[1],data[2]) //prints &{10} &{20} &{30}
}
~~~

### "Hidden" Data in Slices

**Wrong**

~~~go
package main

import "fmt"

func get() []byte {  
    raw := make([]byte,10000)
    fmt.Println(len(raw),cap(raw),&raw[0]) //prints: 10000 10000 <byte_addr_x>
    return raw[:3]
}

func main() {  
    data := get()
    fmt.Println(len(data),cap(data),&data[0]) //prints: 3 10000 <byte_addr_x>
}
~~~

**OK** 

~~~go
package main

import "fmt"

func get() []byte {  
    raw := make([]byte,10000)
    fmt.Println(len(raw),cap(raw),&raw[0]) //prints: 10000 10000 <byte_addr_x>
    res := make([]byte,3)
    copy(res,raw[:3])
    return res
}

func main() {  
    data := get()
    fmt.Println(len(data),cap(data),&data[0]) //prints: 3 3 <byte_addr_y>
}
~~~

### Slice Data "Corruption"

**Wrong**

~~~go
package main

import (  
    "fmt"
    "bytes"
)

func main() {  
    path := []byte("AAAA/BBBBBBBBB")
    sepIndex := bytes.IndexByte(path,'/')
    dir1 := path[:sepIndex]
    dir2 := path[sepIndex+1:]
    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAA
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => BBBBBBBBB

    dir1 = append(dir1,"suffix"...)
    path = bytes.Join([][]byte{dir1,dir2},[]byte{'/'})

    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAAsuffix
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => uffixBBBB (not ok)

    fmt.Println("new path =>",string(path))
}
~~~

**OK** 

~~~go
package main

import (  
    "fmt"
    "bytes"
)

func main() {  
    path := []byte("AAAA/BBBBBBBBB")
    sepIndex := bytes.IndexByte(path,'/')
    dir1 := path[:sepIndex:sepIndex] //full slice expression
    dir2 := path[sepIndex+1:]
    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAA
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => BBBBBBBBB

    dir1 = append(dir1,"suffix"...)
    path = bytes.Join([][]byte{dir1,dir2},[]byte{'/'})

    fmt.Println("dir1 =>",string(dir1)) //prints: dir1 => AAAAsuffix
    fmt.Println("dir2 =>",string(dir2)) //prints: dir2 => BBBBBBBBB (ok now)

    fmt.Println("new path =>",string(path))
}
~~~

### "Stale" Slices

Multiple slices can reference the same data. This can happen when you create a new slice from an existing slice, for example. If your application relies on this behavior to function properly then you'll need to worry about "stale" slices.

At some point adding data to one of the slices will result in a new array allocation when the original array can't hold any more new data. Now other slices will point to the old array (with old data).

~~~go
package main

import "fmt"

func main() {
	s1 := []int{1,2,3}
	fmt.Println(len(s1),cap(s1),s1) //prints 3 3 [1 2 3]

	s2 := s1[1:]
	fmt.Println(len(s2),cap(s2),s2) //prints 2 2 [2 3]

	for i := range s2 { s2[i] += 20 }

	//still referencing the same array
	fmt.Println(s1) //prints [1 22 23]
	fmt.Println(s2) //prints [22 23]

	s2 = append(s2,4)

	for i := range s2 { s2[i] += 10 }

	//s1 is now "stale"
	fmt.Println(s1) //prints [1 22 23]
	fmt.Println(s2) //prints [32 33 14]
}
~~~

### Type Declarations and Methods

**Wrong**

~~~go
package main

import "sync"

type myMutex sync.Mutex

func main() {  
    var mtx myMutex
    mtx.Lock() //error
    mtx.Unlock() //error  
}
~~~

**OK** 

~~~go
package main

import "sync"

type myLocker struct {  
    sync.Mutex
}

func main() {  
    var lock myLocker
    lock.Lock() //ok
    lock.Unlock() //ok
}
~~~

### Interface type declarations also retain their method sets

~~~go
package main

import "sync"

type myLocker sync.Locker

func main() {  
    var lock myLocker = new(sync.Mutex)
    lock.Lock() //ok
    lock.Unlock() //ok
}
~~~

### Breaking Out of "for switch" and "for select" Code Blocks

~~~go
package main

import "fmt"

func main() {  
    loop:
        for {
            switch {
            case true:
                fmt.Println("breaking out...")
                break loop
            }
        }

    fmt.Println("out!")
}
~~~

### Iteration Variables and Closures in "for" Statements

**Wrong**

~~~go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    data := []string{"one","two","three"}

    for _,v := range data {
        go func() {
            fmt.Println(v)
        }()
    }

    time.Sleep(3 * time.Second)
    //goroutines print: three, three, three
}
~~~

**OK** 

~~~go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    data := []string{"one","two","three"}

    for _,v := range data {
        vcopy := v //
        go func() {
            fmt.Println(vcopy)
        }()
    }

    time.Sleep(3 * time.Second)
    //goroutines print: one, two, three
}
~~~

**OK Another solution**

~~~go
package main

import (  
    "fmt"
    "time"
)

func main() {  
    data := []string{"one","two","three"}

    for _,v := range data {
        go func(in string) {
            fmt.Println(in)
        }(v)
    }

    time.Sleep(3 * time.Second)
    //goroutines print: one, two, three
}
~~~

### Deferred Function Call Argument Evaluation

~~~go
package main

import "fmt"

func main() {  
    var i int = 1

    defer fmt.Println("result =>",func() int { return i * 2 }())
    i++
    //prints: result => 2 (not ok if you expected 4)
}
~~~

### Deferred Function Call Execution

**Wrong**

~~~go
package main

import (  
    "fmt"
    "os"
    "path/filepath"
)

func main() {  
    if len(os.Args) != 2 {
        os.Exit(-1)
    }

    start, err := os.Stat(os.Args[1])
    if err != nil || !start.IsDir(){
        os.Exit(-1)
    }

    var targets []string
    filepath.Walk(os.Args[1], func(fpath string, fi os.FileInfo, err error) error {
        if err != nil {
            return err
        }

        if !fi.Mode().IsRegular() {
            return nil
        }

        targets = append(targets,fpath)
        return nil
    })

    for _,target := range targets {
        f, err := os.Open(target)
        if err != nil {
            fmt.Println("bad target:",target,"error:",err) //prints error: too many open files
            break
        }
        defer f.Close() //will not be closed at the end of this code block
        //do something with the file...
    }
}
~~~

**OK** 

~~~go
package main

import (  
    "fmt"
    "os"
    "path/filepath"
)

func main() {  
    if len(os.Args) != 2 {
        os.Exit(-1)
    }

    start, err := os.Stat(os.Args[1])
    if err != nil || !start.IsDir(){
        os.Exit(-1)
    }

    var targets []string
    filepath.Walk(os.Args[1], func(fpath string, fi os.FileInfo, err error) error {
        if err != nil {
            return err
        }

        if !fi.Mode().IsRegular() {
            return nil
        }

        targets = append(targets,fpath)
        return nil
    })

    for _,target := range targets {
        func() {
            f, err := os.Open(target)
            if err != nil {
                fmt.Println("bad target:",target,"error:",err)
                return
            }
            defer f.Close() //ok
            //do something with the file...
        }()
    }
}
~~~

### Failed Type Assertions

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    var data interface{} = "great"

    if data, ok := data.(int); ok {
        fmt.Println("[is an int] value =>",data)
    } else {
        fmt.Println("[not an int] value =>",data) 
        //prints: [not an int] value => 0 (not "great")
    }
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    var data interface{} = "great"

    if res, ok := data.(int); ok {
        fmt.Println("[is an int] value =>",res)
    } else {
        fmt.Println("[not an int] value =>",data) 
        //prints: [not an int] value => great (as expected)
    }
}
~~~

## Advanced Level

### Using Pointer Receiver Methods On Value Instances

**Wrong**

~~~go

package main

import "fmt"

type data struct {  
    name string
}

func (p *data) print() {  
    fmt.Println("name:",p.name)
}

type printer interface {  
    print()
}

func main() {  
    d1 := data{"one"}
    d1.print() //ok

    var in printer = data{"two"} //error
    in.print()

    m := map[string]data {"x":data{"three"}}
    m["x"].print() //error
}
~~~

### Updating Map Value Fields

**Wrong**

~~~go
package main

type data struct {  
    name string
}

func main() {  
    m := map[string]data {"x":{"one"}}
    m["x"].name = "two" //error
}
~~~

**OK** 

~~~go
package main

import "fmt"

type data struct {  
    name string
}

func main() {  
    m := map[string]data {"x":{"one"}}
    r := m["x"]
    r.name = "two"
    m["x"] = r
    fmt.Printf("%v",m) //prints: map[x:{two}]
}
~~~

**OK Another Solution**

~~~go
ackage main

import "fmt"

type data struct {  
    name string
}

func main() {  
    m := map[string]*data {"x":{"one"}}
    m["x"].name = "two" //ok
    fmt.Println(m["x"]) //prints: &{two}
}
~~~

### "nil" Interfaces and "nil" Interfaces Values

**Some Example Code**

~~~go
package main

import "fmt"

func main() {  
    var data *byte
    var in interface{}

    fmt.Println(data,data == nil) //prints: <nil> true
    fmt.Println(in,in == nil)     //prints: <nil> true

    in = data
    fmt.Println(in,in == nil)     //prints: <nil> false
    //'data' is 'nil', but 'in' is not 'nil'
}
~~~

**Wrong**

~~~go
package main

import "fmt"

func main() {  
    doit := func(arg int) interface{} {
        var result *struct{} = nil

        if(arg > 0) {
            result = &struct{}{}
        }

        return result
    }

    if res := doit(-1); res != nil {
        fmt.Println("good result:",res) //prints: good result: <nil>
        //'res' is not 'nil', but its value is 'nil'
    }
}
~~~

**OK** 

~~~go
package main

import "fmt"

func main() {  
    doit := func(arg int) interface{} {
        var result *struct{} = nil

        if(arg > 0) {
            result = &struct{}{}
        } else {
            return nil //return an explicit 'nil'
        }

        return result
    }

    if res := doit(-1); res != nil {
        fmt.Println("good result:",res)
    } else {
        fmt.Println("bad result (res is nil)") //here as expected
    }
}
~~~

### Read and Write Operation Reordering

Go may reorder some operations, but it ensures that the overall behavior in the goroutine where it happens doesn't change. However, it doesn't guarantee the order of execution across multiple goroutines.

~~~go
package main

import (  
    "runtime"
    "time"
)

var _ = runtime.GOMAXPROCS(3)

var a, b int

func u1() {  
    a = 1
    b = 2
}

func u2() {  
    a = 3
    b = 4
}

func p() {  
    println(a)
    println(b)
}

func main() {  
    go u1()
    go u2()
    go p()
    time.Sleep(1 * time.Second)
}
~~~

If you run this code a few times you might see these a and b variable combinations:

~~~
1 
2

3 
4

0 
2

0 
0

1 
4
~~~

The most interesting combination for a and b is "02". It shows that b was updated before a.

If you need to preserve the order of read and write operations across multiple goroutines you'll need to use channels or the appropriate constructs from the "sync" package.

### Preemptive Scheduling

It's possible to have a rogue goroutine that prevents other goroutines from running. It can happen if you have a for loop that doesn't allow the scheduler to run.

~~~go
package main

import "fmt"

func main() {  
    done := false

    go func(){
        done = true
    }()

    for !done {
    }
    fmt.Println("done!")
}
~~~

The for loop doesn't have to be empty. It'll be a problem as long as it contains code that doesn't trigger the scheduler execution.

The scheduler will run after GC, "go" statements, blocking channel operations, blocking system calls, and lock operations. It may also run when a non-inlined function is called.

~~~go
package main

import "fmt"

func main() {  
    done := false

    go func(){
        done = true
    }()

    for !done {
        fmt.Println("not done!") //not inlined
    }
    fmt.Println("done!")
}
~~~

To find out if the function you call in the for loop is inlined pass the "-m" gc flag to "go build" or "go run" (e.g., go build -gcflags -m).

Another option is to invoke the scheduler explicitly. You can do it with the  Gosched() function from the "runtime" package.

~~~go
package main

import (  
    "fmt"
    "runtime"
)

func main() {  
    done := false

    go func(){
        done = true
    }()

    for !done {
        runtime.Gosched()
    }
    fmt.Println("done!")
}

~~~