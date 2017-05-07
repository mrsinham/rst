# Zerogen

Zerogen generates reset method for any structure, allowing to reuse it easily. It has been written to use sync.pool
without fear of tricky bugs as one field not properly reset before reuse.

Just run zerogen and be sure that the reset method will wipe clean your instanciated structure.

## Installation

```sh
go get github.com/mrsinham/zerogen
```

## Usage

With the following structure in the mystructure.go file :

```go
package mypackage

type mystructure struct {
        field1 int
        field2 string
}
```

if you run :

```sh
$ zerogen github.com/me/mypackage mystructure
```

you will have this output :

```go
goreset
package mypackage

func (m *mystructure) Reset() {
	m.field1 = 0
	m.field2 = ""
}
```

just add the -w flag to write it to mystructure_reset.go.

Zerogen handles many types as you can see :

```go
package mypackage

import "net/http"

type mystructure struct {
	field1 int
	field2 string
	field3 [5]int
	field4 []string
	field5 chan int
	field6 chan chan string
	field7 *http.Client
}

```

gives you :

```go
goreset
package mypackage

func (m *mystructure) Reset() {
	m.field1 = 0
	m.field2 = ""
	m.field3 = [5]int{}
	m.field4 = nil
	m.field5 = nil
	m.field6 = nil
	m.field7 = nil
}
```

## Reinstanciate fields

Sometimes you don't want to have the zero value, you want to have those already instanciated. 
For this you cant use the **zg:"nonil"** tag. 

```go
package mypackage

import "net/http"

type mystructure struct {
	field1 *http.Client `zg:"nonil"`
	field2 []chan *http.Client `zg:"nonil"`
}

```

gives you :

```go
$ go build && ./zerogen github.com/mrsinham/mypackage mystructure   
goreset
package mypackage

import http "net/http"

func (m *mystructure) Reset() {
        m.field = &http.Client{}
        m.field = make([]chan *http.Client, 0)
}
```

# TODO

- [X] autobuild of dependencies (to be up to date with code)
- [X] interface by composition
- [X] nonil on inherited fields
- [X] inherited of inherited fields of inherited...
- [X] detection of Reset() method on composition
- [ ] detection of reset method of fields