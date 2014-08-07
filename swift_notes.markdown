# Swift Notes

## switch-case with where
```
let vegetable = "red pepper"
var vegetableComment: String = ""

switch vegetable {
    
case "celery":
    vegetableComment = "Add some raisins and make ants on a log"
    
case "cucumber", "watercress":
    vegetableComment = "That would make a good tea sandwich"

case let x where x.hasSuffix("pepper"):
    vegetableComment = "Is it a spicy \(x) ?"
    
default:
    vegetableComment = "Everything tastes good in soup"
}

println(vegetableComment)
```

## Same Function Name with Same Parameters

### In Others Failure
```
void abc(int a, int b)
void abc(int c, int d)
```

### Swift OK
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


println(total(productPrice: 10, numberOfTimes: 20))
println(total(productSize: 10, numberOfTimes: 20))
println(total(30, 20))

```

## willSet and didSet
```
var triangle: EquilateralTriangle {
willSet {
    //println("triangle willSet")
    square.sideLength = newValue.sideLength
}
}
    
var square: Square {
didSet {
    triangle.sideLength = square.sideLength
}
}
```

## Enum
### toRaw and fromRaw 
```
enum Rank: Int {
    case Ace = 1
    case Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten
    case Jack, Queen, King
    
    func desc() -> String {
        switch self {
        case .Ace: return "ace"
        case .Jack: return "jack"
        case .Queen: return "quene"
        case .King: return "king"
        default: return String(self.toRaw())
        }
    }
}


let ace = Rank.Ace
println(ace.desc())
println(ace.toRaw())

if let tmp = Rank.fromRaw(20) {
    println(tmp.desc())
}
else {
    println("not a rank")
}

```

### No Row Type, No toRaw and fromRaw
```
enum Suit {
    case Spades, Hearts, Diamonds, Clubs
    
    func desc() -> String {
        switch self {
        case .Clubs: return "clubs"
        case .Diamonds: return "diamonds"
        case .Hearts: return "hearts"
        case .Spades: return "spades"
        }
    }
}


let spades = Suit.Spades
println(spades.toRaw()) // error
```

### Instance of Enum
```
enum ServerResponse {
    case Result(String, String)
    case Error(String)
}

let success = ServerResponse.Result("6:00 am", "8:09 pm")
let error = ServerResponse.Error("Out of cheese")
let success2 = ServerResponse.Result("abc", "def")

switch success {
case let .Result(a, b): println("from \(a) to \(b)")
case let .Error(error): println(error)
}

switch success2 {
case let .Result(a, b): println("from \(a) to \(b)")
case let .Error(error): println(error)
}
```
`success` and `success2` are ___Different___ instances.


## Struct
```
struct Card {
    var rank: Rank
    var suit: Suit
    
    func desc() -> String {
        return "The \(rank.desc()) of \(suit.color().desc()) \(suit.desc())"
    }
}

let threeOfSpades = Card(rank: Rank.Three, suit: Suit.Spades)
println(threeOfSpades.desc())
```

__“One of the most important differences between structures and classes is that structures are always copied when they are passed around in your code, but classes are passed by reference value.”__

~~* __Struct: _Pass by Value_ __~~

~~* __Class: _Pass by Reference_ __~~

Note: Like Java, Swift copy the reference value when class instance is assigned to variables or constant, or when passed to functions. Variables is passed by reference with `inout`.


## Protocol like Java Interface

```
class abc {
    
}

protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}

class SimpleClass : abc, ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    
    var anotherProperty: Int = 69105
    
    func adjust()  {
        simpleDescription += " Now 100% adjusted."
    }
}

var a = SimpleClass()
a.adjust()
println(a.simpleDescription)

a.simpleDescription = "abc"
println(a.simpleDescription)

struct SimpleStructure : ExampleProtocol {
    var simpleDescription: String = "A simple structure"
    
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }

}

var b = SimpleStructure()
b.adjust()
println(b.simpleDescription)

```

* It can not assign a initialized value for protocol properties. Just with `{ get }` or `{ get set }`.

	```
	protocol ExampleProtocol {
	    var simpleDescription: String = "" <-- Error
	    mutating func adjust()
	}
	```	
* In Java, Class and Enum `implements` Interface. In Swift, Class, Struct, and Enum confirm(`:`) protocol.
* Class can extend another class and confirm a protocol. `class SimpleClass : abc, ExampleProtocol`.
* Function in protocol can __NOT__ have body, like Interface in Java 7.

	```
	protocol ExampleProtocol {
	    var simpleDescription: String { get }
	    mutating func adjust()
	    
	    mutating func hello() -> String { <-- Error
	      return "hello, world!!"
	    }
	}
	```
* Use `mutating` to override protocol's function in `struct`, but `Not` in `class`

## Extendsion
Use `extension` to extend existing type.

```
extension Int : ExampleProtocol {
    var simpleDescription: String {
        return "The number is \(self)"
    }
    
    mutating func adjust() {
        self += 42
    }
}

println(7.simpleDescription)
```

## Generics
```
func repeat<T>(item: T, times: Int) -> [T] {
    var ret = [T]()
    
    for i in 1...times {
        ret += item
    }
    return ret
}

for s in repeat("abc", 4) {
    println(s)
}
```

```
enum OptionalValue<T> {
    case None
    case Some(T)
}

var possibleInteger: OptionalValue<Int> = .None
possibleInteger = .Some(100)
```

