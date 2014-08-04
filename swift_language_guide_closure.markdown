# Swift Language Guide - Closure

## Syntax

```
{ (parameters) -> return type in 
	statemetns
}
```

```
func mySort<T>(inout values: [T], compare: (T, T) -> Bool) {
    for var i = 0; i < countElements(values); i++ {
        for var j = i + 1; j < countElements(values); j++ {
            if compare(values[i], values[j]) {
                (values[i], values[j]) = (values[j], values[i])
            }
        }
    }
}

func myDump<T>(values: [T]) {
    for elm in values {
        print("\(elm) ")
    }
    println()
}

var a = [3, 2, 8, 5, 9, 7, 12, 4]

myDump(a)

mySort(&a, { (v1: Int, v2: Int) -> Bool in
        println("(\(v1), \(v2))")
        return v1 > v2
    
})

myDump(a)
```

### Inferring Type From Context

```
func mySort<T>(inout values: [T], compare: (T, T) -> Bool) {
    for var i = 0; i < countElements(values); i++ {
        for var j = i + 1; j < countElements(values); j++ {
            if compare(values[i], values[j]) {
                (values[i], values[j]) = (values[j], values[i])
            }
        }
    }
}

func myDump<T>(values: [T]) {
    for elm in values {
        print("\(elm) ")
    }
    println()
}

var a = [3, 2, 8, 5, 9, 7, 12, 4]

myDump(a)

mySort(&a, { v1, v2 in
    println("(\(v1), \(v2))")
    return v1 > v2
})

myDump(a)
```

### Implicit Returns from Single-Expression Closure

```
func mySort<T>(inout values: [T], compare: (T, T) -> Bool) {
    for var i = 0; i < countElements(values); i++ {
        for var j = i + 1; j < countElements(values); j++ {
            if compare(values[i], values[j]) {
                (values[i], values[j]) = (values[j], values[i])
            }
        }
    }
}

func myDump<T>(values: [T]) {
    for elm in values {
        print("\(elm) ")
    }
    println()
}

var a = [3, 2, 8, 5, 9, 7, 12, 4]

myDump(a)

mySort(&a, { v1, v2 in v1 > v2 } )

myDump(a)
```

### Shorthand Argument Names

```
func mySort<T>(inout values: [T], compare: (T, T) -> Bool) {
    for var i = 0; i < countElements(values); i++ {
        for var j = i + 1; j < countElements(values); j++ {
            if compare(values[i], values[j]) {
                (values[i], values[j]) = (values[j], values[i])
            }
        }
    }
}

func myDump<T>(values: [T]) {
    for elm in values {
        print("\(elm) ")
    }
    println()
}

var a = [3, 2, 8, 5, 9, 7, 12, 4]

myDump(a)

mySort(&a, { $0 > $1 })

myDump(a)
```

### Operator Functions (Extreme Shorthand)

```
func mySort<T>(inout values: [T], compare: (T, T) -> Bool) {
    for var i = 0; i < countElements(values); i++ {
        for var j = i + 1; j < countElements(values); j++ {
            if compare(values[i], values[j]) {
                (values[i], values[j]) = (values[j], values[i])
            }
        }
    }
}

func myDump<T>(values: [T]) {
    for elm in values {
        print("\(elm) ")
    }
    println()
}

var a = [3, 2, 8, 5, 9, 7, 12, 4]

myDump(a)

mySort(&a, >)

myDump(a)
```

### Closure on Assignment

```
let a: Int = { () -> Int in
    return 10
}()

let b: Int = { (x: Int) -> Int in
    return x + 20
}(20)
println(b)

let c: Int = {
    return $0 + 20
}(20)
println(c)

let d: Int = { $0 + 20 }(20)
println(20)
```


## Trailing Closures

The closure expression __MUST__ be the function's __FINAL__ argument.

```
func mySort<T>(inout values: [T], compare: (T, T) -> Bool) {
    for var i = 0; i < countElements(values); i++ {
        for var j = i + 1; j < countElements(values); j++ {
            if compare(values[i], values[j]) {
                (values[i], values[j]) = (values[j], values[i])
            }
        }
    }
}

func myDump<T>(values: [T]) {
    for elm in values {
        print("\(elm) ")
    }
    println()
}

var a = [3, 2, 8, 5, 9, 7, 12, 4]

myDump(a)

mySort(&a) {
    $0 > $1
}

myDump(a)
```

## Capturing Values

```
var globalTotal = 0

func test(amount: Int) -> () -> (sub: Int, total: Int) {
    var subTotal = 0
    
    return { () -> (Int, Int) in
        globalTotal += amount
        subTotal += amount
        return (subTotal, globalTotal)
    }
}

let test10 = test(10)

var result = test10()
println("(\(result.sub), \(result.total))")	<-- (10, 10)

result = test10()
println("(\(result.sub), \(result.total))")	<-- (20, 20)


let test7 = test(7)
result = test7()
println("(\(result.sub), \(result.total))")	<-- (7, 27)

result = test7()
println("(\(result.sub), \(result.total))")	<-- (14, 34)

let testAnother10 = test(10)
result = testAnother10()
println("(\(result.sub), \(result.total))")	<-- (10, 44)

```

## Closure Are Reference Type

Assigning a closure to constans or variables is assigning its __REFERENCE__ to them.

```
var globalTotal = 0

func test(amount: Int) -> () -> (sub: Int, total: Int) {
    var subTotal = 0
    
    return { () -> (Int, Int) in
        globalTotal += amount
        subTotal += amount
        return (subTotal, globalTotal)
    }
}

let testTen = test(10)
var rs = testTen()
println("(\(rs.sub), \(rs.total))")	<-- (10, 10)

let testTenAnother = testTen			<-- Assign reference to testTenAnother
rs = testTenAnother()
println("(\(rs.sub), \(rs.total))")	<-- (20, 20)

```

