<p align="center">
  <img src="./logo.png" height="150">
</p>

<h1 align="center">Go</h1>

<p align="center">
  Go is a high-level general purpose programming language that is statically typed and compiled.
</p>

<p align="right">
  <a href="../#tech-stack">Main Page ↖</a>
</p>

### Contents

1. [Understanding Go](#-understanding-go)
   - [Essentials](#-essentials)
     - [Packages](#-packages)
     - [Modules](#-modules)
   - [Go CLI](#-go-cli)
     - [Module and Dependency Management](#-module-and-dependency-management)
     - [Running & Building](#-running--building)
   - [Project Structure Examples](#-project-structure-examples)
     - [#1. An executable Go project with no extra packages.](#-1-an-executable-go-project-with-no-extra-packages)
     - [#2. An executable Go project with extra nested packages.](#-2-an-executable-go-project-with-extra-nested-packages)
     - [#3. A separate non-executable Go library project.](#-3-a-separate-non-executable-go-library-project)
2. [Basics](#-basics)
   - [Basic Data Types](#-basic-data-types)
   - [Declaring Variables](#-declaring-variables)
   - [String Formatting](#-string-formatting)
   - [Type Casting](#-type-casting)
3. [Functions](#-functions)
   - [The `defer` Keyword](#-the-defer-keyword)
   - [Closures](#-closures)
   - [Pass-by Value/Reference](#-pass-by-valuereference)
4. [Pointers](#-pointers)
5. [Data Structures](#-data-structures)
   - [Structs](#-structs)
     - [Struct Tags](#-struct-tags)
   - [Arrays](#-arrays)
   - [Slices](#-slices)
   - [Maps](#-maps)
6. [Methods](#-methods)
7. [Generics](#-generics)
8. [Interfaces](#-interfaces)
   - [Interface Composition](#-interface-composition)
9. [Concurrency](#-concurrency)
   - [Goroutines](#-goroutines)
     - [`WaitGroup`](#-waitgroup)
   - [Channels](#-channels)
     - [Channel Status](#-channel-status)
     - [The `select` Statement](#-the-select-statement)
     - [Read-Only and Write-Only Channels](#-read-only-and-write-only-channels)
     - [The "Done Channel" Pattern](#-the-done-channel-pattern)

<br>

---

## 🔶 Understanding Go

### 🔷 Essentials

#### 🔻 Packages

In Go, code is organized into packages. A package is a collection of Go files in the same directory that are compiled together. Each Go file starts with a package statement that defines the package it belongs to.

There are two types of packages:

- Main package:
  > Files with the `package main` statement define a main package. The main package must contain a `main()` function, which is the starting point of the application.
- Library package:
  > Files with a package statement other than `main` define a library package. Library packages can be imported and used by other packages. These packages are used to organize code into reusable components.

> [!NOTE]
> Only the main package can be executed directly as a standalone program. Library packages can not run on their own. They are meant to be imported and used by other packages.

<br>

#### 🔻 Modules

A module is a collection of related Go packages. It is defined by a go.mod file, which specifies the module's name/path and its dependencies.

<br>

### 🔷 Go CLI

#### 🔻 Module and Dependency Management

- `go mod init <module-name>`: Initialize a new Go module.
  > Creates a `go.mod` file to manage dependencies.
  >
  > The module name should match the intended import path. Usually, it is the URL of the repository where the module is being hosted (`go mod init github.com/user/repo`).
- `go mod tidy`: Clean and verify dependencies.
  > Adds missing dependencies required by the code and removes unused ones.
  >
  > Updates `go.mod` and `go.sum` to accurately reflect the actual imports in use.
- `go get <module>@version`: Update a dependency.
  > Downloads a module and updates it to the specified version.
  >
  > Updates `go.mod` and `go.sum` accordingly.
- `go clean -modcache`: Clear the local module cache.
  > Deletes all downloaded modules from the module cache directory (`$GOPATH/pkg/mod`).

<br>

#### 🔻 Running & Building

- `go run <file>...`: Compile and execute code directly.
  > Compiles and runs the specified Go files.
- `go build`: Compile code into a binary.
  > Builds the Go source files in the current directory and produces an executable.
  >
  > If the package does not include a `main()` function, no binary will be created.
- `go install <module>`: Install a binary.
  > Installs the specified module as a binary in the `$GOPATH/bin` directory.

<br>

### 🔷 Project Structure Examples

#### 🔻 #1. An executable Go project with no extra packages.

1. Create a directory called `myapp`.
2. Initialize a project (module):
   ```sh
   go mod init github.com/username/myapp
   ```
3. Create a `main.go` file:

   ```go
   package main

   import "fmt"

   func main() {
   	fmt.Println("Hello, World!")
   }
   ```

4. Create a different Go file (called `calculate.go`) under the same package:

   ```go
   package main

   func add(a, b int) int {
   	return a + b
   }

   func subtract(a, b int) int {
   	return a - b
   }
   ```

5. Update the `main.go` file to use the functions defined in `calculate.go`:

   > You don't need to import the functions if they are defined in the same package. They are already part of the package.

   ```go
   package main

   import "fmt"

   func main() {
   	fmt.Println(add(1, 2))      // 3
   	fmt.Println(subtract(5, 3)) // 2
   }
   ```

> Overall directory structure:
>
> ```sh
> myapp/
>   ├── go.mod
>   ├── main.go
>   └── calculate.go
> ```

> [!NOTE]
> A folder can only have files that belong to the same package.

> [!NOTE]
> You cannot have multiple functions with the same name under a single package, even if they are in different files.

<br>

#### 🔻 #2. An executable Go project with extra nested packages.

> Overall directory structure:
>
> ```sh
> myapp/
>   ├── go.mod
>   ├── main.go
>   └── calculate/
>           ├── basic.go
>           └── advanced.go
> ```
>
> The `go.mod` file remains in the root directory and handles the dependencies for the entire project. You don't need separate `go.mod` files for nested packages.

1. main.go:

   > You need to import the nested packages because they are not part of the importing package (`main` in this case).

   > When you import nested packages, you need to use their full paths relative to the module path. `github.com/username/myapp` -> `/calculate` = `github.com/username/myapp/calculate`.

   ```go
   package main

   import (
   	"fmt"

   	"github.com/username/myapp/calculate"
   )

   func main() {
   	fmt.Println(calculate.Add(1, 2))      // 3
   	fmt.Println(calculate.Subtract(5, 3)) // 2

   	fmt.Println(calculate.Square(5))      // 25
   	fmt.Println(calculate.SquareRoot(25)) // 5
   }
   ```

2. calculate/

   - basic.go:

     ```go
     package calculate

     func Add(a, b int) int {
     	return a + b
     }

     func Subtract(a, b int) int {
     	return a - b
     }
     ```

   - advanced.go:

     ```go
     package calculate

     import "math"

     func Square(x int) int {
     	return x * x
     }

     func SquareRoot(x int) float64 {
     	return math.Sqrt(float64(x))
     }
     ```

> [!IMPORTANT]
> In Go, only identifiers (variables, fields, functions, types etc.) that start with an uppercase letter are exported and can be accessed from other packages.

<br>

#### 🔻 #3. A separate non-executable Go library project.

> Overall directory structure:
>
> ```sh
> mylib/
>   ├── go.mod
>   ├── mylib.go
>   └── generate/
>           ├── name.go
>           └── surname.go
> ```

1. Create a directory called `mylib` for the library project.
2. Initializing a library module:
   ```sh
   go mod init github.com/username/mylib
   ```
3. mylib.go:

   ```go
   package mylib

   import (
   	"fmt"

   	"github.com/username/mylib/generate"
   )

   func SayHi() {
   	fmt.Printf("Hi %s!\n", generate.Name())
   }

   func SayBye() {
   	fmt.Printf("Bye %s!\n", generate.Surname())
   }

   func GetFullName() string {
   	return fmt.Sprintf("%s %s", generate.Name(), generate.Surname())
   }
   ```

4. generate/:

   - name.go:

     ```go
     package generate

     func Name() string {
     	return "John"
     }
     ```

   - surname.go:

     ```go
     package generate

     func Surname() string {
     	return "Doe"
     }
     ```

<br>

---

Todo:

> #4. Using an external library/package in a Go project.

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Basics

### 🔷 Basic Data Types

- Boolean:

  | Keyword | Values         |
  | ------- | -------------- |
  | `bool`  | `true`/`false` |

  > Zero value (default): `false`

- Numeric:

  | Keyword        | Size            | Values                                      |
  | -------------- | --------------- | ------------------------------------------- |
  | `uint8`/`byte` | 8-bit           | 0 to 255                                    |
  | `uint16`       | 16-bit          | 0 to 65535                                  |
  | `uint32`       | 32-bit          | 0 to 4294967295                             |
  | `uint64`       | 64-bit          | 0 to 18446744073709551615                   |
  |                |                 |                                             |
  | `int8`         | 8-bit           | -128 to 127                                 |
  | `in16`         | 16-bit          | -32768 to 32767                             |
  | `int32`/`rune` | 32-bit          | -2147483648 to 2147483647                   |
  | `int64`        | 64-bit          | -9223372036854775808 to 9223372036854775807 |
  |                |                 |                                             |
  | `float32`      | 32-bit          | -3.4e+38 to 3.4e+38                         |
  | `float64`      | 64-bit          | -1.7e+308 to +1.7e+308                      |
  |                |                 |                                             |
  | `uint`         | 32 bit / 64 bit | uint32 / uint64                             |
  | `int`          | 32 bit / 64 bit | int32 / int64                               |

  > Zero value (default): `0`

- String:

  | Keyword  | Value                                  |
  | -------- | -------------------------------------- |
  | `string` | "anything surrounded by double quotes" |

  > Zero value (default): `""`

  More about strings:

  > Technically, a string is a read-only slice of bytes.
  >
  > ```go
  > str := "abcd" // [97 98 99 100]
  >
  > fmt.Printf("str[0]: %v, type: %T\n", str[0], str[0]) // str[0]: 97, type: uint8
  >
  > for i, v := range str {
  > 	fmt.Println(i, v)
  > }
  > ```
  >
  > ```sh
  > 0 97
  > 1 98
  > 2 99
  > 3 100
  > ```

  > Not all characters are represented by a single byte.
  >
  > ```go
  > str := "é"
  > fmt.Printf("len(str): %v\n", len(str)) // len(str): 2
  > ```
  >
  > > `len()` returns byte count not character count.

  > In Go, strings are encoded in UTF-8 format. In other words, a string represents a sequence of characters encoded as UTF-8 bytes.
  >
  > UTF-8 supports a wide range of characters and symbols, and it uses a **variable-length encoding scheme**. This means characters can be represented by one or more bytes (up to 4), depending on their Unicode code point.
  >
  > | First code point | Last code pint | Byte 1   | Byte 2   | Byte 3   | Byte 4   |
  > | ---------------- | -------------- | -------- | -------- | -------- | -------- |
  > | 0                | 127            | 0yyyzzzz |          |          |          |
  > | 128              | 2,047          | 110xxxyy | 10yyzzzz |          |          |
  > | 2,048            | 65,535         | 1110wwww | 10xxxxyy | 10yyzzzz |          |
  > | 65,536           | 1,114,111      | 11110uvv | 10vvwwww | 10xxxxyy | 10yyzzzz |
  >
  > (all this is just to save space, since most characters can be stored with one or two bytes instead of always using four).
  >
  > For example, the character "`é`" has a Unicode code point of `U+00E9` (233 in decimal). Since 233 is in the range 128-2047, it is represented in UTF-8 using two bytes.
  >
  > ```go
  > str := "é" // [195 169]
  >
  > fmt.Printf("str[0]: %v\n", str[0]) // str[0]: 195
  > fmt.Printf("str[1]: %v\n", str[1]) // str[1]: 169
  > ```
  >
  > Proof (decoding):
  >
  > > 195 is `11000011` in binary, and 169 is `10101001`.
  > >
  > > Matching the bytes using the table above:
  > >
  > > - First byte: `11000011` (110xxxyy) -> `110` + `00011`.
  > > - Second byte: `10101001` (10yyzzzz) -> `10` + `101001`.
  > > - Extracted result: `00011` + `101001` = `0001101001` (binary) = `233` (decimal).

  > When you iterate over a string with `range`, Go automatically decodes each UTF-8 character and returns its Unicode code point.
  >
  > ```go
  > str := "Héllo" // [72 195 169 108 108 111]
  >
  > for i, v := range str {
  > 	fmt.Println(i, v)
  > }
  > ```
  >
  > ```sh
  > 0 72
  > 1 233
  > 3 108
  > 4 108
  > 5 111
  > ```

  > or

  > If you don't care about memory (always uses 4 bytes per character) and want more flexibility (manipulating characters), just convert the string to a slice of runes to make it easier to work with.
  >
  > ```go
  > str := []rune("Héllo") // [72 233 108 108 111]
  >
  > fmt.Printf("str[1]: %v, type: %T\n", str[0], str[0]) // str[1]: 233, type: int32
  > fmt.Println(unsafe.Sizeof(str[0])) // 4 (total str size: 4x5=20 byte)
  >
  > for i, v := range str {
  > 	fmt.Println(i, v)
  > }
  >
  > str[1] = 'e'
  > fmt.Println(string(str)) // Hello
  > ```
  >
  > ```sh
  > 0 72
  > 1 233
  > 2 108
  > 3 108
  > 4 111
  > ```

<br>

### 🔷 Declaring Variables

1.  Using the `var` keyword:

    ```go
    var varName type = value
    ```

    > Variables declared without an explicit initial value are given their zero value.

    - You always have to specify either `type` or `value` (or both):
      > ```go
      > var name string = "John"  // explicit type and value
      > var surname = "Doe"       // type is inferred as string
      > var age int               // declared with explicit type, initialized to zero value (0)
      > ```
    - Declaring multiple variables of the same or different types in a single line:
      > ```go
      > var name, age = "John", 30  // mixed types, types inferred
      > var a, b, c int = 1, 2, 3   // all must be of the declared type
      > var x, y int                // both initialized to zero (0)
      > ```
    - Using grouped declaration syntax to declare variables together:
      > ```go
      > var (
      > 	name    string = "John"
      > 	surname        = "Doe"  // type is inferred
      > 	age     int             // zero value: 0
      > )
      > ```

2.  Using the `:=` syntax:

    ```go
    varName := value
    ```

    > When you use this syntax to declare a variable, you must provide a value for it.
    >
    > `:=` can only be used inside functions (not outside, at the package level).

    - Declaring multiple variables in a single line:
      > ```go
      > name, age := "John", 30
      > ```

3.  Constants:

    > Constants are fixed values that cannot be changed once they are set. They are read-only.

    ```go
    const varName type = value
    ```

    - Constants can be declared without explicit types, but they must be declared with values:
      > ```go
      > const daysInWeek int = 7
      > const hoursInDay = 24
      > ```
    - Declaring multiple constants in a single block:
      > ```go
      > const (
      > 	daysInWeek = 7
      > 	hoursInDay = 24
      > )
      > ```

    > Computations on constants are done at compile time, not at runtime.

<br>

> [!NOTE]
> You can not re-declare a variable that has already been declared in the same scope.

<br>

### 🔷 String Formatting

| Placeholder | Description                                | Usage                                    | Output               |
| ----------- | ------------------------------------------ | ---------------------------------------- | -------------------- |
| `%v`        | Default format for any value.              | `("%v", 123)`                            | `123`                |
| `%+v`       | Struct format with field names.            | `("%+v", Person{Name: "John", Age: 35})` | `{Name:John Age:35}` |
| `%#v`       | Go syntax representation of the value.     | `("%#v", []int{1, 2, 3})`                | `[]int{1, 2, 3}`     |
|             |                                            |                                          |                      |
| `%s`        | String without quotes.                     | `("%s", "hello")`                        | `hello`              |
| `%q`        | Quoted string.                             | `("%q", "hello")`                        | `"hello"`            |
|             |                                            |                                          |                      |
| `%d`        | Integer in base 10.                        | `("%d", 123)`                            | `123`                |
| `%b`        | Integer in base 2.                         | `("%b", 123)`                            | `1111011`            |
| `%o`        | Integer in base 8.                         | `("%o", 123)`                            | `173`                |
| `%x`        | Integer in base 16 with lowercase letters. | `("%x", 123)`                            | `7b`                 |
| `%X`        | Integer in base 16 with uppercase letters. | `("%X", 123)`                            | `7B`                 |
| `%c`        | Integer in Unicode code point.             | `("%c", 65)`                             | `A`                  |
|             |                                            |                                          |                      |
| `%f`        | Decimal format for floating-point numbers. | `("%f", 123.456)`                        | `123.456000`         |
| `%e`        | Scientific notation with lowercase e.      | `("%e", 123.456)`                        | `1.234560e+02`       |
| `%E`        | Scientific notation with uppercase E.      | `("%E", 123.456)`                        | `1.234560E+02`       |
|             |                                            |                                          |                      |
| `%t`        | Boolean value.                             | `("%t", true)`                           | `true`               |
|             |                                            |                                          |                      |
| `%p`        | Hexadecimal memory address.                | `("%p", &x)`                             | `0xc000098040`       |
|             |                                            |                                          |                      |
| `%T`        | Type of the value.                         | `("%T", 123)`                            | `int`                |
| `%%`        | Literal `%` sign.                          | `("%%")`                                 | `%`                  |

> Examples:
>
> ```go
> func main() {
> 	name := "John"
> 	age := 35
> 	char := 97
> 	fmt.Printf("Name: %s, Age: %d, Char: %c ", name, age, char) // Name: John, Age: 35, Char: a
> }
> ```
>
> ```go
> func main() {
> 	pi := 3.14159
> 	fmt.Printf("Pi: %.2f", pi) // Pi: 3.14
> }
> ```

<br>

### 🔷 Type Casting

In Go, type casting refers to explicitly converting a value from one type to another compatible type without any structural change.

```go
func main() {
	x := 25.5
	fmt.Printf("type of x: %T; value of x: %v \n", x, x) // type of x: float64; value of x: 25.5

	y := int(x)
	fmt.Printf("type of y: %T; value of y: %v", y, y) // type of y: int; value of y: 25
}
```

> [!NOTE]
> You can use type casting only between compatible types.

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Functions

- Declaring a function:
  ```go
  func add(a int, b int) int {
  	return a + b
  }
  ```
- Calling a function:
  ```go
  result := add(2, 3) // result = 5
  ```

Extra:

- Functions can have multiple return values:
  ```go
  func swap(a, b int) (int, int) {
  	return b, a
  }
  ```
  ```go
  x, y := 1, 2
  x, y = swap(x, y) // x = 2, y = 1
  ```
- Ignoring return values:
  > Ignoring return values is useful when you don't need to use the return values of a function.
  ```go
  x, _ = getCoords()
  ```
- Named return values:
  > When you have named return values, you can use the return keyword without specifying the values. It will automatically return the named return values. However, using the return keyword without specifying the values can hurt readability, so it is not recommended.
  ```go
  func calculate(x int) (result int) {
  	result = x * x
  	return
  }
  ```
- Variadic functions:
  > Variadic functions are functions that can accept a variable number of arguments.
  ```go
  func sum(numbers ...int) int {
  	total := 0
  	for _, number := range numbers {
  		total += number
  	}
  	return total
  }
  ```
- First-class functions:

  > First-class functions are functions that can be assigned to variables, passed as arguments to other functions, and returned as values from other functions.

  ```go
  func main() {
  	price := 100.00

  	fmt.Printf("Regular user price: $%.2f\n", applyDiscount(price, regularDiscount)) //  Regular user price: $90.00
  	fmt.Printf("VIP user price: $%.2f\n", applyDiscount(price, vipDiscount))         //  VIP user price: $80.00
  }

  func vipDiscount(price float64) float64 {
  	return price * 0.8 // discount 20%
  }

  func regularDiscount(price float64) float64 {
  	return price * 0.9 // discount 10%
  }

  func applyDiscount(price float64, strategy func(float64) float64) float64 {
  	return strategy(price)
  }
  ```

- Anonymous functions:
  > Anonymous functions are functions that don't have a name.
  ```go
  func main() {
  	func() {
  		fmt.Println("Hello, world!")
  	}() // <-- Immediately invoking the anonymous function
  }
  ```
  - Assigning the anonymous function to a variable:
    ```go
    add := func(a, b int) {
    	fmt.Println(a + b)
    }
    add(1, 2) // 3
    ```

<br>

### 🔷 The `defer` Keyword

The `defer` keyword allows us to execute a function or line of code at the end of the current function's execution. This is useful for cleaning up resources, such as closing files or connections.

```go
func main() {
	defer sayBye()

	fmt.Println("1")

	defer fmt.Println("2")

	fmt.Println("3")

	fmt.Println(myFunc())
}

func myFunc() string {
	defer fmt.Println("4")
	return "5"
}

func sayBye() {
	fmt.Println("bye")
}
```

> Output:
>
> ```sh
> 1
> 3
> 4
> 5
> 2
> bye
> ```
>
> Multiple deferred statements are executed in last-in-first-out (LIFO) order. This means that the most recently deferred function is executed first.

<br>

### 🔷 Closures

Closures are functions that can access and manipulate variables from their outer scope, even after the outer function has finished executing.

```go
func add() func(int) int {
	sum := 0

	return func(number int) int {
		sum += number
		return sum
	}
}

func main() {
	count1 := add()
	count2 := add()

	fmt.Println(count1(1)) // 1
	fmt.Println(count1(2)) // 3
	fmt.Println(count1(3)) // 6

	fmt.Println(count2(2)) // 2
	fmt.Println(count2(4)) // 6
	fmt.Println(count2(6)) // 12
}
```

> Each call to `add()` returns a new closure with its own independent `sum` variable, allowing `count1` and `count2` to maintain separate internal states.

<br>

### 🔷 Pass-by Value/Reference

- Pass-by-Value

  > In Go, functions use `pass-by-value` for arguments. This means that when you pass a value to a function, Go creates a copy of that value and passes the copy to the function. The function works with this copy, so changes made to the parameter inside the function do not affect the original value.

  ```go
  func main() {
  	num := 5
  	increment(num)
  	fmt.Println("Outside:", num) // Outside: 5
  }

  func increment(num int) {
  	num++
  	fmt.Println("Inside:", num) // Inside: 6
  }
  ```

- Pass-by-Reference with Pointers

  > If you need to modify the original value, you can use pointers. A pointer holds the memory address of a value. When you pass a pointer to a function, you're passing the address of the value, not a copy of it. This allows the function to modify the original value.

  ```go
  func main() {
  	num := 5
  	increment(&num)
  	fmt.Println("Outside:", num) // Outside: 6
  }

  func increment(num *int) {
  	*num++
  	fmt.Println("Inside:", *num) // Inside: 6
  }
  ```

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Pointers

Pointers in Go allow you to directly reference the memory address of a variable. This allows you to directly access and modify the value stored in that memory location.

- `&`: To get the memory address of a variable.
- `*`: To access or modify the value at the memory address.

```go
func main() {
	// Declaring a variable:
	a := 10

	fmt.Println(a)        // 10
	fmt.Printf("%T\n", a) // int

	// Declaring a pointer and assigning it the address of 'a':
	ptr := &a

	fmt.Println(ptr)        // 0xc0000a4010 (memory address of 'a')
	fmt.Printf("%T\n", ptr) // *int
	fmt.Println(*ptr)       // 10 (value at the memory address)

	// Changing the value at the memory address:
	*ptr = 20

	fmt.Println(a) // 20
}
```

> By using pointers for function parameters, you pass the memory address of a variable rather than a copy of its value. This changes Go's default pass-by-value behavior, allowing the function to modify the original data directly.
>
> ```go
> func main() {
> 	num := 10
> 	fmt.Println(num) // 10
>
> 	resetVal(num)
> 	fmt.Println(num) // 10
>
> 	resetPtr(&num)
> 	fmt.Println(num) // 0
> }
>
> func resetVal(val int) {
> 	val = 0
> }
>
> func resetPtr(val *int) {
> 	*val = 0
> }
> ```

> Pointers as function parameters also let you work with large data structures more efficiently. No unnecessary copying means better performance.
>
> - Without pointer:
>
>   ```go
>   func main() {
>   	arr := [5]int{1, 2, 3, 4, 5}
>   	fmt.Printf("mem addr (arr): %p, arr: %v\n", &arr, arr)
>
>   	myFunc(arr)
>   }
>
>   func myFunc(items [5]int) [5]int {
>   	fmt.Printf("mem addr (items): %p, items: %v\n", &items, items)
>
>   	return items
>   }
>   ```
>
>   ```sh
>   mem addr (arr): 0xc00011e000, arr: [1 2 3 4 5]
>   mem addr (items): 0xc00011e060, items: [1 2 3 4 5]
>   ```
>
> - With pointer:
>
>   ```go
>   func main() {
>   	arr := [5]int{1, 2, 3, 4, 5}
>   	fmt.Printf("mem addr (arr): %p, arr: %v\n", &arr, arr)
>
>   	myFunc(&arr)
>   }
>
>   func myFunc(items *[5]int) [5]int {
>   	fmt.Printf("mem addr (items): %p, items: %v\n", items, *items)
>
>   	return *items
>   }
>   ```
>
>   ```sh
>   mem addr (arr): 0xc00011e000, arr: [1 2 3 4 5]
>   mem addr (items): 0xc00011e000, items: [1 2 3 4 5]
>   ```
>
> As you can see, when using pointers the array is not being copied (they have the same memory addresses). This is especially useful with big datasets, as it reduces memory usage and speeds up processing.

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Data Structures

### 🔷 Structs

A struct is a sequence of uniquely named elements, called fields, each of which has a name and a type.

- Defining a struct:
  ```go
  type person struct {
  	name string
  	age  int
  }
  ```
- Creating a struct instance:
  ```go
  user := person{name: "John", age: 35}
  ```
  or zero valued:
  ```go
  var user person
  ```
- Accessing and modifying struct fields:
  ```go
  fmt.Println(user.name) // John
  user.age = 30
  ```

Extra:

- Embedded and nested structs:

  ```go
  type address struct {
  	city    string
  	state   string
  	zipCode string
  }

  type contact struct {
  	phone string
  	email string
  }

  type person struct {
  	name    string
  	age     int
  	address         // Embedded struct
  	contact contact // Nested struct

  }

  func main() {
  	user := person{
  		name: "John",
  		age:  35,
  		address: address{
  			city:    "Los Angeles",
  			state:   "California",
  			zipCode: "00000",
  		},
  		contact: contact{
  			phone: "(000) 000-0000",
  			email: "john@example.com",
  		},
  	}

  	fmt.Println(user)               // {John 35 {Los Angeles California 00000} {(000) 000-0000 john@example.com}}
  	fmt.Println(user.city)          // Los Angeles
  	fmt.Println(user.contact.phone) // (000) 000-0000
  }
  ```

<br>

#### 🔻 Struct Tags

Struct tags are metadata attached to struct fields. They are typically used by packages like `encoding/json` to control how fields are encoded or decoded.

> By default, Go uses struct field names as-is when encoding to JSON or other formats. Suppose you need to export the fields, which requires the field names to be capitalized. This often results in capitalized field names in the JSON output, which may not match your desired JSON structure/naming convention. Struct tags allow you to specify how the fields should be named in other formats.

<br>

```go
type User struct {
	Id    int    `json:"id"`
	Name  string `json:"name"`
	Email string `json:"email"`
}
```

> Tags use backticks and the format: `key:"value"`, and are not limited to `json`.

```go
func main() {
	myUser := User{
		Id: 1, Name: "John", Email: "hello@example.com",
	}

	fmt.Printf("myUser: %+v\n", myUser) // myUser: {Id:1 Name:John Email:hello@example.com}

	jUser, _ := json.Marshal(myUser) // Converting struct to JSON

	fmt.Printf("jUser: %+v\n", string(jUser)) // jUser: {"id":1,"name":"John","email":"hello@example.com"}
}
```

<br>

### 🔷 Arrays

In Go, array is a fixed-size sequence of elements of a single type.

- Declaring an array:
  ```go
  var arr [5]int // zero valued
  ```
  - Initializing at the time of declaration:
    ```go
    arr := [5]int{1, 2, 3, 4, 5}
    ```
- Accessing array elements:
  ```go
  fmt.Println(arr[0]) // 1
  ```
- Modifying array elements:
  ```go
  arr[0] = 100
  fmt.Println(arr[0]) // 100
  ```

Extra:

- Letting the compiler decide the size of the array:
  ```go
  arr := [...]int{3, 5, 6, 2, 1} // [5]int
  ```
- Initializing specific indexes of an array:
  > When you use the `index: value` syntax, you specify the value for a particular index in the array. This allows you to skip some indices (with zero values) and directly assign values to others.
  ```go
  arr := [...]int{3, 5, 4: 6, 10: 1, 2, 1}
  fmt.Println(arr) // [3 5 0 0 6 0 0 0 0 0 1 2 1]
  ```
- Multi-dimensional arrays:
  ```go
  arr2d := [3][2]int{
  	{1, 2},
  	{3, 4},
  	{5, 6},
  }
  fmt.Println(arr2d) // [[1 2] [3 4] [5 6]]
  ```
- Slicing an array:
  > Slicing allows you to create a slice from an array or an existing slice.
  ```go
  arr := [5]int{0, 10, 20, 30, 40}
  fmt.Println(arr[1:3]) // [10 20]
  ```
  Syntax:
  > ```go
  > slice1 := arr[2:4] // From index 2 to index 3
  > slice2 := arr[:4]  // From the start to index 3
  > slice3 := arr[4:]  // From index 4 to the end
  > slice4 := arr[:]   // The entire array
  > ```

<br>

> [!IMPORTANT]
> In Go, array elements are stored in contiguous memory locations, meaning that all elements of an array are stored next to each other in memory.
>
> ```go
> func main() {
> 	arr := [3]int{1, 2, 3}
>
> 	fmt.Printf("&arr[0]: %v\n", &arr[0]) // &arr[0]: 0xc00011e000
> 	fmt.Printf("&arr[1]: %v\n", &arr[1]) // &arr[1]: 0xc00011e008
> 	fmt.Printf("&arr[2]: %v\n", &arr[2]) // &arr[2]: 0xc00011e010
> }
> ```
>
> Here, the type `int` is `int64` (because I'm on a 64-bit system), so each element takes up 8 bytes of memory. The addresses of the elements are spaced 8 bytes apart (0 -> 8 -> 16).

<br>

### 🔷 Slices

In Go, a slice is a dynamically-sized, flexible view into the elements of an array. Unlike arrays, slices can grow and shrink the length during execution.

- Declaring a slice:
  ```go
  var slice []int // zero length
  ```
  - Using the make function:
    > non-zero length & zero valued
    ```go
    slice := make([]int, 5)
    ```
  - Initializing at the time of declaration:
    ```go
    slice := []int{1, 2, 3, 4, 5}
    ```

Extra:

- Appending to a slice:
  ```go
  slice := []int{1, 2, 3}
  slice = append(slice, 4, 5, 6)
  fmt.Println(slice) // [1 2 3 4 5 6]
  ```
- Removing elements from a slice:
  - Removing the `index 2` element:
    > The `...` syntax here is the **"spread"** operator. When you use it after a slice, it "unpacks" the slice so that its elements can be passed as individual arguments.
    ```go
    slice = append(slice[:2], slice[3:]...)
    ```
    > More clear example of spread operator usage:
    >
    > ```go
    > slice := []int{2, 4, 6}
    > otherSlice := []int{1, 3, 5}
    > slice = append(slice, otherSlice...)
    > fmt.Println(slice) // [2 4 6 1 3 5]
    > ```
  - Removing the last element:
    ```go
    slice = slice[:len(slice)-1]
    ```
  - Removing all elements expect the first two:
    ```go
    slice = slice[:2]
    ```

<br>

> [!IMPORTANT]
> Slices have a length, which is the number of elements they contain, and a capacity, which is the size of the underlying array they reference.
>
> If you append elements to a slice beyond its current capacity, Go will handle this automatically by allocating a (2x) larger array and copying the existing elements to it (to the new address). This can be an expensive operation in terms of performance.
>
> ```go
> var slice []int // slice is initially nil, with a length and capacity of 0.
> fmt.Println(len(slice)) // 0
> fmt.Println(cap(slice)) // 0
>
> slice = append(slice, 1, 2, 3, 4)
>
> fmt.Println(len(slice)) // len: 4
> fmt.Println(cap(slice)) // cap: 4
>
> slice = append(slice, 5, 6)
>
> fmt.Println(len(slice)) // len: 6
> fmt.Println(cap(slice)) // cap: 8
> ```
>
> To improve performance, we can predefine the capacity of a slice. Predefining the capacity can help avoid unnecessary reallocations when appending elements.

- Predefining the capacity:

  ```go
  slice := make([]int, 0, 10)

  fmt.Println(slice)      // []
  fmt.Println(len(slice)) // len: 0
  fmt.Println(cap(slice)) // cap: 10
  ```

  > Predefining the capacity only makes sense when you have a reasonable knowledge of how the data will grow or change over time.

<br>

> [!CAUTION]
> Never use `append` on anything other than itself.
>
> ```go
> func main() {
> 	a := make([]int, 5, 7) // 3rd argument is the capacity.
> 	fmt.Println("a:", a) // a: [0 0 0 0 0]
>
> 	b := append(a, 1)
> 	fmt.Println("b:", b) // b: [0 0 0 0 0 1]
>
> 	c := append(a, 2)
>
> 	fmt.Println("a:", a) // a: [0 0 0 0 0]
> 	fmt.Println("b:", b) // b: [0 0 0 0 0 2] <-- b got updated because of c
> 	fmt.Println("c:", c) // c: [0 0 0 0 0 2]
> }
> ```
>
> Here, when creating the `b` slice, the `a` slice has a capacity of `7` and a length of `5`, which means it can add a new element without allocating a new array. So, `b` now references the same array as `a`. The same thing happens when creating `c`. It also references the same array as `a`. At this point, because both `b` and `c` share the same underlying array, appending `2` through `c` updates the `1` that was appended through `b`.
>
> This unexpected behavior would not occur if there were not enough capacity for the new element. In that case, Go would allocate a new array and copy the existing elements to it, resulting in new addresses. But still, it is prone to go unexpected.

<br>

> [!NOTE]
> When you pass a slice to a function, you are passing a reference to the underlying array, not a copy of the array's elements. Any modifications to the elements of the slice within the function will affect the original arrays.
>
> ```go
> func main() {
> 	slice := []int{1, 2, 3}
>
> 	fmt.Println("Before:", slice) // Before: [1 2 3]
>
> 	modifySlice(slice)
>
> 	fmt.Println("After:", slice) // After: [99 2 3]
> }
>
> func modifySlice(s []int) {
> 	s[0] = 99
> }
> ```
>
> While slices behave like pass-by-reference for the data they point to, the slice itself is passed by value. This means if you reassign the slice variable inside the function, it won't affect the original slice variable outside the function.
>
> ```go
> func main() {
> 	slice := []int{1, 2, 3}
>
> 	fmt.Println("Before:", slice) // Before: [1 2 3]
>
> 	reassignSlice(slice)
>
> 	fmt.Println("After:", slice) // After: [1 2 3]
> }
>
> func reassignSlice(s []int) {
> 	s = []int{100, 200, 300}
> 	s[0] = 999
> }
> ```

<br>

### 🔷 Maps

Maps in Go are used to store unordered collections of key-value pairs.

- Declaring & initializing a map:
  > You can't just declare a map with `var m map[string]int` and then assign values to it. If you try to do this, you'll get a `panic: assignment to entry in nil map` error. To make the map ready to use, you need to initialize it (either empty or with values) using the `make` function as shown below.
  ```go
  m := make(map[string]int)
  ```
  - Initializing with values:
    ```go
    m := map[string]int{
    	"one":   1,
    	"two":   2,
    	"three": 3,
    }
    ```
- Accessing map elements:
  ```go
  fmt.Println(m["one"]) // 1
  ```
- Adding elements to a map:
  ```go
  m["four"] = 4
  fmt.Println(m) // map[one:1 two:2 three:3 four:4]
  ```
- Deleting elements from a map:
  ```go
  delete(m, "two")
  fmt.Println(m) // map[one:1 three:3]
  ```
- Modifying map elements:
  ```go
  m["one"] = 100
  fmt.Println(m["one"]) // 100
  ```

Extra:

- Checking if a key exists in a map:
  > The optional second return value is a boolean indicating whether the key was found in the map.
  ```go
  _, ok := m["one"]
  fmt.Println(ok) // true
  ```
  > This is important because in Go, maps always return some value for a key, even if the key does not exist. If you try to access a key that doesn't exist, it will return the zero value for the value type:
  >
  > ```go
  > 	m := map[string]int{
  > 		"zero":  0,
  > 		"one":   1,
  > 		"two":   2,
  > 	}
  >
  > 	fmt.Println(m["three"]) // 0
  > ```
- Clearing all elements from a map:
  ```go
  clear(m)
  ```
- Nested maps:

  ```go
  m2d := make(map[string]map[string]int)

  m2d["a"] = map[string]int{"first": 1}

  fmt.Println(m2d) // map[a:map[first:1]]

  fmt.Println(m2d["a"]["first"]) // 1
  ```

> [!CAUTION]
> In Go, maps need to be initialized before you can use them.
>
> ```go
> func main() {
> 	m2d := make(map[string]map[string]int)
>
> 	m2d["a"]["b"] = 1 // <-- panic: assignment to entry in nil map
>
> 	m2d["a"] = make(map[string]int)
>
> 	m2d["a"]["b"] = 1 // <- ok
>
> 	fmt.Println(m2d["a"]["b"]) // 1
> }
> ```

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Methods

In Go, methods are functions that are associated with a specific type. This means that you can attach methods to a named type and call them on instances of that type.

> [!NOTE]
> You can only define methods on named types. Defining methods directly on unnamed or literal types like `[]int` or `map[string]int` is not allowed.
>
> A named type is any type that you define using the `type` keyword and assign an explicit name to.
>
> ```go
> type MyInt int
> ```
>
> ```go
> type Person struct {
>  name string
>  age  int
> }
> ```

<br>

Syntax:

> ```go
> func (receiver TypeName) MethodName(parameters) returnType {
>    // method body
> }
> ```
>
> - `receiver`: The variable name that represents the instance the method is being called on.
> - `TypeName`: The type the method is attached to.
> - `MethodName`: The name of the method being defined.
> - `parameters`: Optional arguments the method accepts.
> - `returnType`: The type of value the method returns.

<br>

- Defining a method:

  ```go
  type person struct {
  	name string
  	age  int
  }

  func (p person) sayHello() {
  	fmt.Println("Hello, my name is " + p.name)
  }
  ```

- Calling a method:
  ```go
  user := person{name: "John", age: 35}
  user.sayHello() // Hello, my name is John
  ```

<br>

> [!NOTE]
> When manipulating data through a method, we need to use pointers because of the `pass-by-value` rule.
>
> ```go
> func (p *person) changeName(name string) {
> 	p.name = name
> 	// (*p).name = name // This is also valid, but unnecessary in Go.
> 	// Go simplifies pointer access for structs by dereferencing p behind the scenes.
> }
>
> func main() {
> 	user := person{name: "John", age: 35}
>
> 	user.sayHello() // Hello, my name is John
>
> 	user.changeName("Sha'an")
>
> 	user.sayHello() // Hello, my name is Sha'an
> }
> ```
>
> Another example (not a struct):
>
> ```go
> type myCounter int
>
> func (mc *myCounter) increment() {
> 	*mc++
> }
>
> func main() {
> 	count := myCounter(0)
>
> 	count.increment()
> 	count.increment()
>
> 	fmt.Printf("count: %v\n", count) // count: 2
> }
> ```

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Generics

Generics in Go are a way to write reusable code that can work with different types. Instead of writing multiple versions of a function for different data types, you can write one generic function that works with any type.

> Generics maintain type safety by allowing the compiler to check types at compile-time. This prevents many types of runtime errors.

Syntax:

> ```go
> func myFunc[T any](input T) T {
> 	return input
> }
> ```
>
> The type parameters are enclosed in square brackets `[]` and come after the function name.
>
> - Type Parameter:
>   > `T` is a type parameter that can represent any type. You can name it anything, but `T` is commonly used.
> - Constraint:
>   > The `any` constraint means that `T` can be any type (`any` is a shorthand for `interface{}`).

<br>

Example 1:

- Without Generics:

  ```go
  func main() {
  	fmt.Println(addInts(1, 2))       // 3
  	fmt.Println(addFloats(1.5, 2.3)) // 3.8

  }

  func addInts(a, b int) int {
  	return a + b
  }

  func addFloats(a, b float64) float64 {
  	return a + b
  }
  ```

- With Generics:

  ```go
  func main() {
  	fmt.Println(add(1, 2))     // 3
  	fmt.Println(add(1.5, 2.3)) // 3.8

  }

  // Using | specifies a union of the two types, meaning that this constraint allows either type.
  func add[T int | float64](a T, b T) T {
  	return a + b
  }
  ```

Example 2:

```go
func main() {
	fmt.Println(swap(5, 10))            // 10, 5
	fmt.Println(swap(2.5, 5.2))         // 5.2, 2.5
	fmt.Println(swap("Hello", "World")) // "World", "Hello"
}

func swap[T any](a, b T) (T, T) {
	return b, a
}
```

<br>

Example 3:

```go
func main() {
	fmt.Println(swap(2.5, 5))      // 5, 2.5
	fmt.Println(swap("Hello", 99)) // 99, "Hello"
}

func swap[T any, U any](a T, b U) (U, T) {
	return b, a
}
```

<br>

Example 4:

```go
type Number interface {
	int | float64
}

func multiply[T Number](a, b T) T {
	return a * b
}

func main() {
	fmt.Println(multiply(5, 6))             // 30
	fmt.Println(multiply(3.2, 4.1))         // 13.12
	fmt.Println(multiply("Hello", "World")) // string does not satisfy Number (string missing in int | float64) compiler(InvalidTypeArg)
}
```

> [!NOTE]
> Go does not support direct union types. you cannot do something like:
>
> ```go
> type Number int | float64
> ```
>
> Instead, you can use an interface to define a set of types that satisfy a certain behavior:
>
> ```go
> type Number interface {
> 	int | float64
> }
> ```

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Interfaces

In Go, an interface is a type that specifies a set of method signatures. If a type implements all the methods of an interface, it is said to satisfy that interface.

Interfaces enable flexible and decoupled code, allowing you to define functions that can work with any type that implements the interface.

> A type can implement multiple interfaces, and an interface can be satisfied by multiple types.

- Defining an interface:

  ```go
  type shape interface {
  	area() float64
  	perimeter() float64
  }
  ```

  > Here, the `shape` interface defines two methods: `area()` and `perimeter()`. Any type that implements these methods can be considered a `shape`.

- Implementing an interface

  > Both `circle` and `rect` satisfy the `shape` interface because they implement all required methods. The implementation happens automatically, no explicit declaration is needed.

  ```go
  type circle struct {
  	radius float64
  }

  func (c circle) area() float64 {
  	return math.Pi * c.radius * c.radius
  }

  func (c circle) perimeter() float64 {
  	return 2 * math.Pi * c.radius
  }
  ```

  ```go
  type rect struct {
  	width  float64
  	height float64
  }

  func (r rect) area() float64 {
  	return r.width * r.height
  }

  func (r rect) perimeter() float64 {
  	return 2*r.width + 2*r.height
  }
  ```

- Using the interface:

  > Functions can accept interface types as parameters, allowing them to work with any type that satisfies the interface.

  ```go
  func printArea(s shape) {
    fmt.Println("Area:", s.area())
    fmt.Println("Perimeter:", s.perimeter())
  }

  func main() {
    myCircle := circle{radius: 3}
    printArea(myCircle)
    // Area: 28.274333882308138
    // Perimeter: 18.84955592153876

    myRect := rect{width: 3, height: 4}
    printArea(myRect)
    // Area: 12
    // Perimeter: 14
  }
  ```

<br>

> [!NOTE]
> An empty interface (`interface{}`) require zero methods, so every type satisfies it. Also, you can use the alias `any` instead of `interface{}`.
>
> ```go
> var x interface{}
>
> func main() {
> 	x = 5
> 	x = "hello"
> 	x = true
>
> 	fmt.Println(x) // true
> }
> ```
>
> You can use empty interfaces with type switches to handle values of different types at runtime:
>
> ```go
> func describe(i interface{}) {
> 	switch v := i.(type) {
> 	case string:
> 		fmt.Println("It's a string:", v)
> 	case int:
> 		fmt.Println("It's an integer:", v)
> 	default:
> 		fmt.Println("Unknown type")
> 	}
> }
>
> func main() {
> 	x := 5
> 	describe(x) // It's an integer: 5
> }
> ```
>
> The `x.(T)` syntax is known as a "type assertion". It can be used directly with any type, including named types and pointer types:
>
> ```go
> func main() {
> 	var x interface{} = "Hello"
>
> 	val, ok := x.(string)
> 	if ok {
> 		fmt.Println("It's a string:", val)
> 	} else {
> 		fmt.Println("Not a string")
> 		// val is zero value here
> 	}
> }
> ```
>
> The `ok` variable is `true` if the type assertion succeeds, and `false` if it fails. When `ok` is `false`, `val` will have the zero-value.
>
> > Here is another example showing usage with the `shape` interface defined above in the "Defining an interface" section:
> >
> > ```go
> > // ...
> >
> > func identifyShape(s shape) {
> > 	val, ok := s.(circle)
> > 	if ok {
> > 		fmt.Println("Shape is a circle, val:", val)
> > 	} else {
> > 		fmt.Println("Shape is not a circle")
> > 		// val is zero value here
> > 	}
> > }
> >
> > func main() {
> > 	// ...
> >
> > 	identifyShape(myCircle) // Shape is a circle, val: {3}
> > }
> > ```

<br>

### 🔷 Interface Composition

Interface composition allows you to create new interfaces by combining existing ones.

```go
type Reader interface {
	Read(p []byte) (n int, err error)
}

type Writer interface {
	Write(p []byte) (n int, err error)
}

type ReadWriter interface {
	Reader
	Writer
}
```

> The `ReadWriter` interface requires both `Read` and `Write` methods. Any type that implements both automatically satisfies the `ReadWriter` interface.

> [!NOTE]
> If multiple interfaces are composed and they contain methods with the same name, those methods must have identical signatures in all interfaces.

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Concurrency

### 🔷 Goroutines

Goroutines are a feature in Go that allows you to run functions concurrently.

In general, we can split the execution of a program into two types of routines:

- Main Routine:
  > The main routine is the initial goroutine that starts when a Go program begins execution. It's the entry point of the program, defined by the main function in the main package. When the main function exits, the program terminates, so any running goroutines will also be stopped.
- Child Routines:
  > A child routine is any goroutine that is spawned by the main routine or other goroutines. These are created using the `go` keyword followed by a function call. Child routines run concurrently with the main routine and each other.

<br>

```go
func main() {
	go expensiveFunc("Hello")

	fmt.Println("Main")

	time.Sleep(1700 * time.Millisecond)
}

func expensiveFunc(text string) {
	for i := 0; i < 4; i++ {
		time.Sleep(500 * time.Millisecond)
		fmt.Println(text, i)
	}
}
```

> Output:
>
> ```sh
> Main
> Hello 0
> Hello 1
> Hello 2
> ```

> The `time.Sleep` inside the main function is used to give enough time for the goroutines to finish before the main function exits. Without this, the program would exit immediately after the main routine has done its job.
>
> The output shows results for only 3 iterations, not 4 as specified in the for loop. This is because we have a `time.Sleep` of 1.7 seconds (1700 milliseconds), which is less than the minimum of 2 seconds (2000 milliseconds) needed for 4 iterations (4 \* 500 ms = 2000 ms) in the `expensiveFunc` function.
>
> Btw, using `time.Sleep` to wait for goroutines to finish is not a good practice.

<br>

#### 🔻 `WaitGroup`

A `WaitGroup` is a part of the `sync` package that's used to wait for a collection of goroutines to finish executing.

> When you launch goroutines, they run concurrently, and the main function might exit before the goroutines finish. `WaitGroup` ensures that the program waits until all the work is done before exiting or moving on.

<br>

- `wg.Add(n)`: Tells the `WaitGroup` to wait for `n` additional operations.
- `wg.Done()`: Decrements the counter (`-1`). Usually deferred at the start of each goroutine.
- `wg.Wait()`: Blocks until the counter goes back to 0.

```go
func main() {
	var wg sync.WaitGroup

	wg.Add(1)
	go expensiveFunc("Hello", &wg)

	fmt.Println("Main")

	wg.Wait()

	fmt.Println("End")
}

func expensiveFunc(text string, wg *sync.WaitGroup) {
	defer wg.Done()

	for i := range 4 {
		time.Sleep(500 * time.Millisecond)
		fmt.Println(text, i)
	}
}
```

> Output:
>
> ```sh
> Main
> Hello 0
> Hello 1
> Hello 2
> Hello 3
> End
> ```

<br>

You can also use the business logic functions inside anonymous functions to keep the business logic cleaner:

> ```go
> func main() {
> 	var wg sync.WaitGroup
>
> 	wg.Add(1)
> 	go func(wg *sync.WaitGroup) {
> 		defer wg.Done()
> 		expensiveFunc("Hello")
> 	}(&wg)
>
> 	fmt.Println("Main")
>
> 	wg.Wait()
>
> 	fmt.Println("End")
> }
>
> func expensiveFunc(text string) {
> 	for i := range 4 {
> 		time.Sleep(500 * time.Millisecond)
> 		fmt.Println(text, i)
> 	}
> }
> ```
>
> This way, we keep the `expensiveFunc` function clean and focused on its own logic, while the goroutine management is handled separately.

<br>

### 🔷 Channels

Channels in Go are a way to communicate between goroutines. They are used to send and receive values between goroutines.

- Declaring a channel:
  > The type of a channel specifies what kind of data it can carry.
  ```go
  ch := make(chan int)
  ```
- Sending values to a channel:
  ```go
  ch <- 10
  ```
- Receiving values from a channel:
  ```go
  myVar := <-ch
  ```
- Closing a channel:
  > Closing a channel is a way to signal to the receiving goroutine that it should stop waiting for values to be sent to it. It's important to close a channel when you're done sending values to avoid a `deadlock`.
  ```go
  close(ch)
  ```

> [!NOTE]
> Channel synchronization ensures that communication between goroutines is properly coordinated. It guarantees that data sent between goroutines is not lost and that goroutines wait for each other when necessary, maintaining the correct order and timing of operations.
>
> - Send Operation: `ch <- value`
>   > When a goroutine sends a value into a channel, it will block (pause execution) until another goroutine is ready to receive that value from the channel.
> - Receive Operation: `value := <-ch`
>   > When a goroutine tries to receive a value from a channel, it will block until a value is available to be received.

<br>

Example:

```go
func main() {
	ch := make(chan string)

	go expensiveFunc("Hello", ch)

	fmt.Println("Main")

	for range 4 {
		fmt.Println(<-ch)
	}

  fmt.Println("End")
}

func expensiveFunc(text string, ch chan string) {
	for i := range 4 {
		time.Sleep(500 * time.Millisecond)
		ch <- text + " " + fmt.Sprint(i)
	}
}
```

> Output:
>
> ```sh
> Main
> Hello 0
> Hello 1
> Hello 2
> Hello 3
> End
> ```

> Here, we don't need any additional mechanism in the `main` function to wait for the goroutine to finish. The `<-ch` operation in the main function blocks until there is a value to receive from the channel. This blocking behavior synchronizes the main function with the `expensiveFunc` goroutine. Each iteration of the loop in the main function waits for a corresponding send operation from `expensiveFunc`. Btw, we are not forced to use a loop here. We can use `fmt.Println(<-ch)` directly 4 times, one after the other, it does the same thing.
>
> In this specific example, we don't strictly need to close the channel because the main function will only receive a fixed number of messages (4 in this case) and then it stops.
>
> Here is the modified version of it that needs to be closed explicitly:
>
> ```go
> func main() {
> 	ch := make(chan string)
>
> 	go expensiveFunc("Hello", ch)
>
> 	fmt.Println("Main")
>
> 	for msg := range ch {
> 		fmt.Println(msg)
> 	}
>
> 	fmt.Println("Done.")
> }
>
> func expensiveFunc(text string, ch chan string) {
> 	defer close(ch)
>
> 	for i := range 4 {
> 		time.Sleep(500 * time.Millisecond)
> 		ch <- text + " " + fmt.Sprint(i)
> 	}
> }
> ```
>
> > The `for msg := range ch { ... }` syntax essentially performs a `msg := <-ch` operation under the hood, which is where the blocking behavior occurs.
> >
> > `range` doesn't know how many values a channel will receive, it can be infinite. To stop the loop, we must explicitly close the channel to indicate that no more values are coming.

<br>

#### 🔻 Channel Status

The receiver of a channel can check the status of the channel using the second return value of the receive operation.

```go
val, ok := <-ch
```

The second return value (`ok`) is a boolean that indicates whether the channel is closed or not.

- `true`: The channel is closed and no more values can be sent to it.
- `false`: The channel is open and values can be sent to it.

```go
func main() {
	ch := make(chan int)

	go func(ch chan int) {
		ch <- 1
		ch <- 2
		close(ch)
	}(ch)

	for {
		val, ok := <-ch
		if !ok {
			fmt.Println("Channel is closed.")
			break
		}
		fmt.Println("Received:", val)
	}
}
```

> When you `range` over a channel, the loop will automatically break when the channel is closed. But if you're using a manual receive loop, checking `ok` helps you know when to stop receiving.

<br>

#### 🔻 The `select` Statement

The `select` statement in Go is a control structure that allows you to work with multiple channels simultaneously. It's similar to a `switch` statement but is specifically designed for channel operations.

- The `select` statement listens to several channels.
- It runs the first case that's ready to proceed.
- If multiple cases are ready, Go randomly picks one to execute.
- If there's no `default` case, it will block and wait until a case becomes ready.
- If a `default` case is present and no other cases are ready, it executes the `default` case immediately without blocking.

```go
func main() {
	// Preparation:
	ch1 := make(chan int)
	ch2 := make(chan int)
	ch3 := make(chan int)

	go func(ch chan int) {
		time.Sleep(time.Second)
		ch <- 1
	}(ch1)

	go func(ch chan int) {
		time.Sleep(time.Second * 3)
		ch <- 2
	}(ch2)

	go func(ch chan int) {
		time.Sleep(time.Second * 2)
		ch <- 3
	}(ch3)

	time.Sleep(time.Second * 5)

	// Usage:
	select {
	case val1 := <-ch1:
		fmt.Println(val1)
	case val2 := <-ch2:
		fmt.Println(val2)
	case val3 := <-ch3:
		fmt.Println(val3)
	default:
		fmt.Println("No channels are ready.")
	}

	fmt.Println("Done.")
}
```

> [!NOTE]
> The `select` statement is not a loop, it executes only one case even if multiple are ready, and then breaks the `select` statement.

<br>

#### 🔻 Read-Only and Write-Only Channels

In Go, channels can be restricted to be either `read-only` or `write-only`. This is useful for defining clear communication patterns between goroutines and helps in maintaining code safety and clarity.

- Read-only channels:
  > A read-only channel can only be used to receive values. You cannot send values into a read-only channel.
- Write-only channels:
  > A write-only channel can only be used to send values. You cannot receive values from a write-only channel.

```go
func sendData(ch chan<- int) { // ch is write-only
	ch <- 42
	// can't do: <- ch
	close(ch)
}

func receiveData(ch <-chan int) { // ch is read-only
	fmt.Println(<-ch)
	// can't do: ch <- 24
}

func main() {
	ch := make(chan int)

	go sendData(ch)
	receiveData(ch)
}
```

<br>

#### 🔻 The "Done Channel" Pattern

The "Done Channel" pattern is a technique used to signal the completion/termination of a goroutine. The technique involves using a separate helper channel to indicate when a goroutine should stop its execution.

- Without Done Channel:

  ```go
  func myFunc() {
    for {
      fmt.Println("MyFunc")
    }
  }

  func main() {
    go myFunc()

    time.Sleep(1 * time.Hour)
  }
  ```

  > The `myFunc` goroutine will print "MyFunc" until the main program exits.

- With Done Channel:

  ```go
  func myFunc(doneCh <-chan struct{}) {
    for {
      select {
      case <-doneCh:
        return
      default:
        fmt.Println("MyFunc")
      }
    }
  }

  func main() {
    doneCh := make(chan struct{})

    go myFunc(doneCh)

    time.Sleep(5 * time.Second)
    close(doneCh)

    time.Sleep(1 * time.Hour)
  }
  ```

  > The `myFunc` goroutine will print "MyFunc" for 5 seconds and then return, even though the main program continues to run for an hour.

> [!NOTE]
> The `struct{}` type represents an empty struct in Go. It consumes zero bytes of memory, making it ideal for signaling and control purposes without causing any memory overhead.

<p align="right">
    <a href="#go">back to top ⬆</a>
</p>

<br>
<br>
