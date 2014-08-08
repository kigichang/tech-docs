# Swift Language Guide - Methods


## Instance Methods

Instance Methods are functions in __Class__(`class`), __Structure__(`struct`) and __Enumerations__(`enum`).

* The first parameter in method is a local parameter name by default.
* The __Second__ and __Subsequence__ parameters are local and __External__ names by default.
* Use `_` before parameter to disable __External__ name
* `self` is like `this` in Java

	```
	class Point : Printable {
	    
	    var x = 0, y = 0
	    
	    init(x: Int, y: Int) {
	        self.x = x
	        self.y = y
	    }
	    
	    var description : String {
	        return "(\(x), \(y))"
	    }
	    
	    func moveToX(x: Int, andY: Int) {	<-- andY is an external name
	        self.x = x
	        self.y = andY
	    }
	    
	    func moveTo(x: Int, _ y: Int) {	<-- use _ to disable external name
        	self.x = x
        	self.y = y
    	}
	}
	
	
	let point = Point(x: 10, y: 20)
	println(point)
	point.moveToX(30, andY: 40)		<-- invoke with external name
	println(point)
	point.moveTo(50, 60)
	println(point)
	```

### Instance Methods in Structure and Enumerations

__Structure__(`struct`) and __Enumerations__(`enum`) are __Value Type__.

* Functions __MUST__ have `mutating` when modifying properties.
* Can __NOT__ invoking a `mutating` function of a __Constant__ structure or enumerations.

	```
	struct Point : Printable {
	    var x: Double = 0.0, y: Double = 0.0
	    
	    
	    var description : String {
	        return "(\(x), \(y))"
	    }
	    
	    func getX() -> Double {
	        return self.x
	    }
	    
	    func getY() -> Double {
	        return self.y
	    }
	    
	    mutating func moveTo(x: Double, andY y: Double) {	<-- Change x and y property, so MUST have mutating.
	        self.x = x
	        self.y = y
	    }
	    
	    func distance(that: Point) -> Double {
	        let dx = (self.x - that.x)
	        let dy = (self.y - that.y)
	        
	        return sqrt(dx * dx + dy * dy)
	    }
	    
	}
	
	var point1 = Point(x: 10, y: 10)
	var point2 = Point(x: 20, y: 20)
	
	println("point1 = \(point1)")
	println("point2 = \(point2)")
	
	point1.moveTo(10, andY: 20)
	
	println("point1 = \(point1)")
	println("point1.X = \(point1.x)")
	println("point1.Y = \(point1.y)")
	println("Distance between point1 and point2 is \(point1.distance(point2))")
	
	
	let origin = Point(x: 0.0, y: 0.0)		<-- Constant structure
	println("origin = \(origin)")
	println("Distance between point and origin is \(origin.distance(point1))")
	
	origin.moveTo(30, andY: 40)		<-- Error. Can NOT invoke a mutating function of Constant structure.
	```