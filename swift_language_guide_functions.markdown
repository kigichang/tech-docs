# Swift Language Guide - Functions


__Functions without return values does return a special value `Void` and it is simply an empty `tuple`__


## External Parameter Names

```
func join(string s1: String, toString s2: String, withJoiner joiner: String) -> String {
    return s1 + joiner + s2
}

println(join(string: "hello", toString: "world", withJoiner: ", "))
```


```
func total(productPrice price: Int, numberOfTimes times: Int) -> Int {
    return price * times
}

func total(productSize size: Int, numberOfTimes times: Int) -> Int {
    return size * times * 3
}

func total(amount: Int,times: Int) -> Int {
    return amount * times * 2
}

func total(x: Int, y: Int) -> Int {	<- Error
    return x + y
}


println(total(productPrice: 10, numberOfTimes: 20))  <-- 200
println(total(productSize: 10, numberOfTimes: 20))   <-- 600
println(total(10, 20))                               <-- 400

```


### Shorthand External Parameter Names

__Using `#`__ 


```
func total(#productPrice: Int, #numberOfTimes: Int) -> Int {
    return productPrice * numberOfTimes
}

func total(#productSize: Int, #numberOfTimes: Int) -> Int {
    return productSize * numberOfTimes * 3
}

func total(amount: Int,times: Int) -> Int {
    return amount * times * 2
}

println(total(productPrice: 10, numberOfTimes: 20))
println(total(productSize: 10, numberOfTimes: 20))
println(total(10, 20))
```

### Default Values with External Parameter Names

Swift __AUTOMATICALLY__ provides an extenal parameter name for the defaulted parameter.

```
func join(s1: String, s2: String, joiner: String = " ") -> String {
    return s1 + joiner + s2
}

func join(string s1: String, toString s2: String, withJoiner joiner: String = ", ") -> String {
    return s1 + joiner + s2
}

println(join("hello", "world"))

println(join("hello", "world", "-")) <-- Error, MUST use external parameter name (joiner: "-")

println(join("hello", "world", joiner: "-"))

println(join(string: "hello", toString: "world"))

println(join(string: "hello", toString: "world", withJoiner: "@"))
```


## Parameter Passing

### Constants

Paramters are __CONSTANTS__. It can not be assign new value.

```
func abc(a: Int, b: Int) -> Int {
    a = 20	<-- Error
    return a + b
}
```

### Variable

__Using `var`__

```
var def = "def"
func test(var str: String) -> String {
    str = "abc"		<-- str is a local variable, NOT constant. 
    return str
}

println(test(def))	<-- abc
println(def)		<-- still, def

```

### In-Out

* In-Out will change original values, and pass with `&`. 

	```
	func swap(inout a: Int, inout b: Int) {
	    (a, b) = (b, a)
	}
	
	var a = 10, b = 20
	
	println("(a, b) = (\(a), \(b))")
	swap(&a, &b)
	println("(a, b) = (\(a), \(b))")
	
	```


* Can __NOT__ pass __CONSTANT__ and __LITERAL__ value

	```
	func abc(inout a: Int) {
	    a += 1
	}
	
	let a = 10
	abc(&a)      <-- Error
	println(a)
	abc(20)		 <-- Error
	
	```

* Can __NOT__ apply on `var` parameters

	```
	func abc(var inout a: Int) {	<-- inout is an external parameter name
	    a += 1
	}
	
	abc(inout: 20)
	
	func def(inout var a: Int) {	<-- Error
	    a += 1
	}
	```

* Can __NOT__ apply on __VARIADIC__ parameters

	```
	func abc(inout a: Int...) {		<-- Error
	    
	}
	
	```

## Function Type

```
func swap(inout a: Int, inout b: Int) {
    (a, b) = (b, a)
}

var a = 10, b = 20

println("(a, b) = (\(a), \(b))")
swap(&a, &b)
println("(a, b) = (\(a), \(b))")

let mySwap: (inout Int, inout Int) -> () = swap

mySwap(&a, &b)

println("(a, b) = (\(a), \(b))")


```

