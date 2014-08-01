# Swift Language Guide - String and Characters

* Swift’s String type is bridged seamlessly to Foundation’s NSString class.

## Initialize Empty String

```
var str1 = ""
var str2 = String()

var str3: String = nil <-- Error, can't assign nil
```


## Strings are Value Type

Swift’s String type is a __Value Type__. 

When assignment or passing to a function, Swift will copy a __NEW INSTANCE__ from old version.

## String is a collection of Characters

```
for char in "Dog!🐶" {
    println(char)
}

```

## String Interpolation

```
let a = 1.0
let b = 2.0

println("\(a) + \(b) = \(a + b)")
```

## Unicode

`\u` with 4-digital, `\U` with 8-digital

```
let a: Character = "\u0065"
let e = "\U0001F1FA\U0001F1F8"
```

## Extended Grapheme Clusters

“An extended grapheme cluster is a sequence of one or more Unicode scalars that (when combined) produce a single human-readable character.”

__Excerpt From: Apple Inc. “The Swift Programming Language.” iBooks. https://itun.es/tw/jEUH0.l__

```
let a: Character = "\u0065"
let b: Character = "\u00e9"
let c: Character = "\u0301"
let d: Character = "\u0065\u0301"

println(a)
println(b)
println(c)
println(d)

println(d == b)

let e = "\U0001F1FA\U0001F1F8"
println(e)
```

### Count Elements on Extended Grapheme Clusters

```
let e = "\U0001F1FA\U0001F1F8"
println("\(e) length is \(countElements(e))") <-- 🇺🇸 length is 2

var cafe = "cafe"
println("\(cafe) length is \(countElements(cafe))")  <-- cafe length is 4
cafe += "\u0301"
println("\(cafe) length is \(countElements(cafe))")  <-- café length is 5

```

__The Answer is NOT the same with [The Swift Programming Language] on iBook. I use xCode Beta 3.__


## String Equality

```
let a = "abc"
let b = "abc"

println(a == b ? "same" : "Not the same")
```

## Unicode Representation

```
let dog = "Dog‼🐶"

println(dog)

for c in dog.utf8 {
    print("\(c) ")
}
println()

for c in dog.utf16 {
    print("\(c) ")
}
println()

for u in dog.unicodeScalars {
    print("(\(u),\(u.value)) ")
}
println()

```