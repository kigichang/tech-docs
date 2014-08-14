# Swift Language Guide - Properties

* Constant Value Type is mean the constant value, so you can __NOT__ modify any properties in it.
* Constant Reference Type is mean the constant reference value, you can __NOT__ assign a new instance to it, but you can modify variable properties in it. The constant properties can __NOT__ be modified as usual.

## Stored Properties

Stored Property is a __Constant__ or __Variable__ stored as part of an __Instance__ of a particular __Class__ or __Structure__.

* Stored Property is only in Class or Structure.
* Constant stored property can be modified in __Initializers__

	```
	struct Point {
	    let x = 0, y = 0
	    
	    init(x: Int, y: Int) {	<-- Initializer
	        self.x = x
	        self.y = y
	    }
	}
	
	
	let point = Point(x: 30, y: 30)
	
	```

* Can __NOT__ modify any stored properties of  ~~__Constant Instance__~~ __Constant Value Type__ (like structure), even the stored property is a __Variable__.

	```
	struct Point {
	    var x = 0, y = 0	<-- Variable stored properties
	    
	    init(x: Int, y: Int) {
	        self.x = x
	        self.y = y
	    }
	}
	
	
	let point = Point(x: 30, y: 30)		<-- assign as Constant
	
	point.x = 200	<-- Error, Can NOT change variable stored property of an Constant instance
	```
	
	```
	class Point {
	    var x = 0, y = 0    <-- Variable stored properties
	
	    init(x: Int, y: Int) {
	        self.x = x
	        self.y = y
	    }
	}
	
	
	let point = Point(x: 30, y: 30)     <-- assign as Constant
	
	point.x = 200   <-- OK
	```

### Lazy Load

* Use `lazy` before declare a variable. (NOT `@lazy` in _The Swift Programming Lanaguage_. I use Xcode beta 5)
* `lazy` only apply on __Variable (`var`)__. __Constant (`let`)__ will report an error.
* `lazy` is only valid for member of __Structure (`struct`)__ or __Class (`class`)__. 

```
struct Color : Printable {
    var red = 0, green = 0, blue = 0
    init(red: Int, green: Int, blue: Int) {
        println("a color init with (\(red), \(green), \(blue))")
        self.red = red
        self.green = green
        self.blue = blue
    }
    
    var description : String {
        return "(\(red), \(green), \(blue))"
    }
}

struct Point : Printable {
    var x = 0, y = 0
    lazy var color = Color(red: 128, green: 128, blue: 128)	<-- a lazy member data
    
    init(x: Int, y: Int) {
        self.x = x
        self.y = y
    }

    var description : String {
        return "(\(x), \(y))"
    }
}

var point = Point(x: 20, y: 20)		<-- will NOT initialize color

println("\(point) color is \(point.color)")	<-- Initialize color and output: a color init with (128, 128, 128)

lazy var a = { (b: Int) -> Int in	<-- Error, lazy only available in structure or class.
    println("aa")
    return b + 10

}(20)
```


## Computed Properties

Computed Property can be defined in __Class (`class`)__, __Structures (`struct`)__, and __Enumerations (`enum`)__.

* Computed Property can be defined in Class, Structure, and Enumerations.
* Computed Property does __NOT__ store a value actually, instead of providing a __Getter__ and __Setter__ (optional).
* Use `newValue` for shorthand form in __Setter__

	```
	struct Point : Printable {
	    var x = 0.0, y = 0.0
	    
	    var distance: Double {
	        get {
	            return sqrt(x * x + y * y)
	        }
	        set {
	            var tmp = sqrt(newValue * newValue / 2)
	            x = tmp
	            y = tmp
	        }
	    }
	    var description : String {
	        return "position: (\(x), \(y)) and distance: \(distance)"
	    }
	}
	
	
	var point = Point(x: 10, y: 10)
	println("point is \(point)")
	
	point.distance = 5
	println("point is \(point)")
	```

* Computed Property __MUST__ be a variable, __NOT__ a constant, even if it is __Read Only__.
* Read-Only computed property define __Getter__ only, or return value without `get`.

	```
	struct Point : Printable {
	    var x = 0.0, y = 0.0
	    
	    var distance: Double {
	        get {	<-- get can be ignored.
	            return sqrt(x * x + y * y)
	        }
	    }
	    var description : String {
	        return "position: (\(x), \(y)) and distance: \(distance)"
	    }
	}
	
	
	var point = Point(x: 10, y: 10)
	println("point is \(point)")
	
	point.distance = 5	<-- Error. Can NOT assign value
	
	```
* Computed Property on Global and Local Variables

	```
	var x = 0, y = 0
	
	struct Point : Printable {
	    var x = 0, y = 0
	    
	    init() {
	        println("a point init with (0, 0)")
	    }
	    
	    init(x: Int, y: Int) {
	        self.x = x
	        self.y = y
	        
	        println("a point init with (\(x), \(y))")
	    }
	    
	    var description : String {
	        return "(\(x), \(y))"
	    }
	}
	
	
	var a: Point {
	get {
	    println("getter of a is called")
	    return Point(x: x, y: y)
	}
	set {
	    println("setter of a is called")
	}
	}
	
	func testComputed() {
	    x = 20
	    y = 30
	    var b: Point {
	        get {
	            println("getter of b is called")
	            return Point(x: x, y: y)
	        }
	        set {
	            println("setter of b is called")
	        }
	    }
	    
	    println("before printing b")
	    println("b is \(b)")
	    
	    x = 10
	    y = 100
	    
	    println("a is \(a)")
	}
	```



## Property Observers

* `willSet` is called just before a value is stored. `newValue` is used in `willSet` implicitly.
* `didSet` is called immediately after new value is stored. `oldValue` is used in `didSet` implicitly.
* Property Observers can __NOT__ be used with __Getter (`get`)__ and __Setter (`set`)__.
* Initializers will __NOT__ trigger `willSet` and `didSet`
	
	```
	struct Point : Printable {
	    var x: Double = 0.0 {	<-- property observer with stored property
	        willSet {
	            println("a New Value (\(newValue)) will set to X")
	        }
	        
	        didSet {
	            println("the Old Value of X is (\(oldValue))")
	        }
	    }
	    
	    var y = 0.0
	    
	    var distance: Double {		<-- property observer with computed property
	    
	        /*	<-- willSet and didSet can NOT used with get and set
	        get {
	            return sqrt(x * x + y * y)
	        }
	
	        set {
	            var tmp = sqrt(newValue * newValue / 2)
	            x = tmp
	            y = tmp
	        }
	        */
	        
	        willSet {
	            println("a New Value (\(newValue)) will set to distance")
	        }
	
	        didSet {
	            println("the Old Value of distance is (\(oldValue))")
	        }
	    }
	    
	    init(x: Double, y: Double) {
	        self.x = x
	        self.y = y
	        self.distance = sqrt(x * x + y * y)
	    }
	    
	    var description : String {
	        return "position: (\(x), \(y)) and distance: \(distance)"
	    }
	    
	    mutating func tune() {
	        var tmp = sqrt(distance * distance / 2)
	        x = tmp
	        y = tmp
	    }
	}
	
	
	var point = Point(x: 10.0, y: 10.0)	<-- Initializers do NOT trigger observers.
	
	println("set x")
	point.x = 20.0				<-- trigger observers of x
	
	println("set distance")
	point.distance = 100		<-- trigger observers of distance
	
	println("tune point after change distance")
	point.tune()				<-- trigger observers of x
	```

* Property Observers on Global and Local Variables

	```
	var tmpForNew: Int = 0
	var tmpForOld: Int = 0
	
	var a: Int = 10 {
	    willSet {
	        println("the new value for a is \(newValue)")
	        tmpForNew = newValue
	    }
	
	    didSet {
	        println("the old value of a is \(oldValue)")
	        tmpForOld = oldValue
	    }
	}
	
	func testObservers() {
	    
	    a = 30	<-- trigger observers of a
	    println("(\(tmpForNew), \(tmpForOld))")	<-- (30, 10)
	    
	    var b: Int = 20 {
	        willSet {
	            println("the new value for a is \(newValue)")
	            tmpForNew = newValue
	        }
	        
	        didSet {
	            println("the old value of a is \(oldValue)")
	            tmpForOld = oldValue
	        }
	    }
	    
	    b = a + b	<-- trigger observers of b
	    println("(\(tmpForNew), \(tmpForOld))")	<-- (50, 20)
	}
	
	testObservers()
	```
	
