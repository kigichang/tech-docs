# Swift Language Guide - Collection

## Array

### Initialize 

```
var a: [String] = ["a", "b", "c"]
var b: [String] = []
var c = ["a", "b", "c"]
var d = []					<-- OK, NOT suggest

c += "abc"
d += "abc"	<-- Error

var e = [Int]() 	<-- an empty of Integer Array

var f = [Double](count: 3, repeatedValue: 10)	<-- [10.0, 10.0, 10.0]
```

### Operations

```
func dumpArr<T>(arr: [T]) {
    for (i, elm) in enumerate(arr) {
        println("\(i) = \(elm)")
    }
    println()
}

var arr = ["a", "b", "c"]

arr += "abc"					<-- ["a", "b", "c", "abc"]

for str in arr {
    println(str)
}

arr += ["def", "ghi"]			<-- ["a", "b", "c", "abc", "def", "ghi"]
dumpArr(arr)

println(arr[0])

arr[0] = "zzz"					<-- ["zzz", "b", "c", "abc", "def", "ghi"]
dumpArr(arr)

arr[2...4] = ["yyy", "xxx"]		<-- ["zzz", "b", "yyy", "xxx", "ghi"] the 4-index element is removed
dumpArr(arr)

arr[2..<4] = ["mmm", "nnn"]		<-- ["zzz", "b", "mmm", "nnn", "ghi"]
dumpArr(arr)

arr.insert("ggg", atIndex: 3)	<-- ["zzz", "b", "mmm", "ggg", "nnn", "ghi"]
dumpArr(arr)

arr.removeAtIndex(0)			<-- ["b", "mmm", "ggg", "nnn", "ghi"]
dumpArr(arr)

arr.removeLast()				<-- ["b", "mmm", "ggg", "nnn"]
dumpArr(arr)

arr = []						<-- empty array
println(arr.count)				<-- 0
```

__arr[2...4] = ["yyy", "xxx"]		<-- ["zzz", "b", "yyy", "xxx", "ghi"] the 4-index element is removed___



## Dictionary

### Initialize

```
var b:[String: Int] = ["a": 1, "b": 2]
var c = [String: String]()
var a = ["a": 1, 2: "b"]	<-- OK, NOT Suggest
```

### Operations

```
func dumpDict<T, U>(dict: [T:U]) {
    for (key, value) in dict {
        println("(\(key), \(value))")
    }
    println()
}

var dict = [String: Int]()

dict["a"] = 1
dict["b"] = 2

dumpDict(dict)

let old1 = dict.updateValue(3, forKey: "a")
println("old value is \(old1)")

let old2: Int! = dict.updateValue(4, forKey: "c")
println("old value is \(old2)")

if let old3 = dict.updateValue(5, forKey: "d") {
    println("old value is \(old3)")
}
else {
    println("No element in dict")
}

dict["a"] = nil		<-- remove element for key "a"
dumpDict(dict)

if let old4 = dict.removeValueForKey("a") {
    println("old value of \"a\" is \(old4)")
}
else {
    println("No element in dict")
}

for key in dict.keys {
    println("(\(key), \(dict[key]))")
}

for key in dict.keys {
    println("(\(key), \(dict[key]))")
}

var keys = Array(dict.keys)				<-- Array
for (i, elm) in enumerate(keys) {
    println("\(i) = \(elm)")
}

var values = Array(dict.values)			<-- Array
for (i, elm) in enumerate(values) {
    println("\(i) = \(elm)")
}

dict = [:]								<-- empty dictionary
println(dict.count)						
```

* dict._updateValue_ return __OPTIONAL VALUE__
* dict._removeValueForKey_ return __OPTIONAL VALUE__