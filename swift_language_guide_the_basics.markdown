# Swift Language Guide - The Basics

## Constants and Variables

```
let a = 10 <-- Constant
var b = 20 <-- Variable
```

```
var a =  10, b = 1.0, c = "abc" <-- multiple assignments in a line

println("\(a), \(b), \(c)")

```

## String with Variables

```
var a =  10, b = 1.0, c = "abc"

var str = "\(a), \(b), \(c)"

println(str)
```

## Multiple Statements in a Line

Use semicolon `;`

```
let a = 10; println(a)
```

## Data Type

### Integer

Swift has 8, 16, 32, 64 bit forms with __signed__ and __unsigned__.

Most usage: Int and UInt is platform __Dependence__.

* Int on 32-bit is Int32
* Int on 64-bit is Int64

The default type is `Int` if declaration without type annotation like this

```
let a = 10 <-- the type is Int
let b = UInt8.min <-- the type is UInt8 and values is 0
```

__Declaration:__

```
let decimalInt = 17
let binaryInt = 0b10001
let octalInt = 0o21
let hexInt = 0x11

println(decimalInt)
println(binaryInt)
println(octalInt)
println(hexInt)

let oneMillion = 1_000_000
println(oneMillion)
```


### Floating-Point

The same with common language support `Float` and `Double`.

The default type is `Double` if declaration without type annotation like this

```
let a = 3.1415 <-- the type is Double
```

__Declaration:__

* `e` for decimal 10^exp: `1.25e2 = 1.25 x 10^2`
* `p` for hexadecimal 2^exp: `0xFp2 = 15 x 2^2 = 60.0`

```
let justOverOneMillion = 1_000_000.000_000_1
println(justOverOneMillion)

let a = 1.25e2
let b = 1.25e-2

println(a)
println(b)

let c = 0xFp2
let d = 0xFp-2

println(c)
println(d)

let e = 0xC.3p0
println(e)
```


### Conversion

__Floating-point to Integer will truncate point part__

```
let a: Int16 = 10
let b: Int8  = 2
let c  = a + Int16(b)

let d = 3
let pi = Double(d) + 0.1415926

println(pi)

let e = Int(pi)
println(e)
```

### Type Aliases

```
typealias MyInt = Int64

println(MyInt.max)
```

### Boolean

__Type annotation is `Bool`__

The similar type `Boolean` is `UInt8` __NOT__ a boolean type `Bool`

```
let t: Bool = true
let f: Bool = false
let ok = true
let error: Bool = 10  <-- Error

println("Bool only has \(t) and \(f)")

let a: Boolean = 10  <-- type is UInt8
let b: UInt8 = 20
let c = a + b
println(c)

```

### Tuples

```
let http404 = (404, "Not Found")

println("\(http404.0), \(http404.1)")

let (code, msg) = http404

println("\(code): \(msg)")

let (onlyCode, _) = http404

println("\(onlyCode)")

let (_, onlyMsg) = http404

println("\(onlyMsg)")

let another404 = (code: 404, msg: "Not Found")

println("\(another404.code), \(another404.msg)")

func test(a: Int, b: String) -> (code: Int, msg: String) {
    return (a, b) 				<-- OK
    return (code: a, msg: b)	<-- OK
    return (msg: b, code: a)	<-- OK
    return (b, a)				<-- Error
}

let t = test(200, "OK")

println("\(t.0), \(t.1)")
println("\(t.code), \(t.msg)")

```

## Optional

* There _is_ a value, and it equals _x_

OR

* There _isn't_ a value at all


### If statement and Forced Unwrapping

```
let str = "123"
let num = str.toInt()

if num {
    println("\(str) is a number \(num)")
}
else {
    println("\(str) is NOT a number")
}

```

__Optional Binding__

```
let str = "123"
if let tmp = str.toInt() {
    println("\(str) is a number \(tmp)")
}
else {
    println("\(str) is NOT a number")
}
```

### Unwrap

__use `!` to unwrap optionals__

```
var test: String? = "abc"

println(test)	<-- abc
println(test!)	<-- abc

test = nil

println(test)	<-- nil
println(test!)	<-- fatal error: unexpectedly found nil while unwrapping an Optional value
```

__Implicitly Unwrapped Optionals__

```
func toBeOrNotToBe(be: Bool) -> String? {
    return be ? "ToBe" : nil
}

let be: String! = toBeOrNotToBe(false) <-- implicitly unwrapped
println(be)							<-- nil
println(toBeOrNotToBe(false))		<-- nil
println(toBeOrNotToBe(true))		<-- ToBe

```

