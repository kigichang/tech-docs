# Swift Language Guide - Class and Structure


## Common

* Define properties to store values
* Define method to provide functionality
* Define subscriptions to provide access to their values using subscript syntax
* Define initializers to set up their initial state
* Be extended to expand their functionality beyond a default implementation
* Confirm to protocols to provide standard functionality of certain kind

## Structure NOT

* Inheritance
* Type Casting
* De-initializers
* Reference Counting (Structure is Value Type, Class is Reference Type)


## Initialize Syntax

__Initialization MUST use External Names__

__`init` is with External Names implicitly__

### Structure

```
struct Color {
    let red = 0.0, green = 0.0, blue = 0.0
}

let black = Color()
let white = Color(red: 255.0, green: 255.0, blue: 255.0)
let c1 = Color(255.0, 255.0, 255.0) <-- Error, MUST use External Names
```

```
struct Color {
    let red = 0.0, green = 0.0, blue = 0.0
    
    init(red: Double) {		<-- Constructor
        self.red = red
    }
}


let black = Color()		<-- Error

let white = Color(red: 255.0, green: 255.0, blue: 255.0)	<-- Error
let c1 = Color(255.0, 255.0, 255.0)	<-- Error
let c2 = Color(255.0)					<-- Error
let c2 = Color(red: 255.0)				<-- OK, remember to USE External Names
```

### Class

```
class Color {
    let red = 0.0, green = 0.0, blue = 0.0
}


let black = Color()		<-- OK
let white = Color(red: 255.0, green: 255.0, blue: 255.0)	<-- Error, Constructor is NOT exist.
```

```
class Color {
    let red = 0.0, green = 0.0, blue = 0.0
    
    init(red: Double, green: Double, blue: Double) {
        self.red = red
        self.green = green
        self.blue = blue
    }    
}

let black = Color()		<-- Error, Constructor is NOT exist.
let white = Color(red: 255.0, green: 255.0, blue: 255.0)	<-- OK
```

```
class Color {
    let red = 0.0, green = 0.0, blue = 0.0
    
    init(red: Double, green: Double, blue: Double) {
        self.red = red
        self.green = green
        self.blue = blue
    }
    
    init() {
        self.red = 255.0
        self.green = 255.0
        self.blue = 255.0
    }
}


let black = Color()		<-- OK
let white = Color(red: 255.0, green: 255.0, blue: 255.0)	<-- OK

```


## Initializer

* _Designated initializer_ : primary initializers. May have MANY.
* _Convenience initializer_ : secondary initializers. 
	
	* Use `convenience`
	* MUST invoke designated initializer


```
class Color {
    let red = 0.0, green = 0.0, blue = 0.0
    
    init(red: Double, green: Double, blue: Double) {
        self.red = red
        self.green = green
        self.blue = blue
    }
    
    convenience init() {	<-- Error, convenience initializer MUST invoke designated one
        self.red = 255.0
        self.green = 255.0
        self.blue = 255.0
    }
}
```

```
class Color {
    let red = 0.0, green = 0.0, blue = 0.0
    
    init(red: Double, green: Double, blue: Double) {
        self.red = red
        self.green = green
        self.blue = blue
    }
    
    convenience init() {	<-- OK, convenience initializer invoke a designated one
        self.init(red: 255.0, green: 255.0, blue: 255.0)
    }
}
```


### Inheritance

* _Designated_ and _Convenience_ initializers in subclass MUST invoke _Designated_ one of superclass
* Subclass can NOT override _Convenience_ initializer of superclass to _Designated_
* Be careful with Initializer Chain Problem in _Convenience_ Initializer of subclass.

__Use Cases__

* No initializer defined in subclass and use _Designated_ one of superclass

	```
	class Color {
	    let red = 0.0, green = 0.0, blue = 0.0
	    
	    init(red: Double, green: Double, blue: Double) {	<-- Designated
	        self.red = red
	        self.green = green
	        self.blue = blue
	    }
	}
	
	class AlphaColor : Color {
	    let alpha = 255.0
	}
	
	
	let c1 = AlphaColor(red: 255.0, green: 128.0, blue: 255.0)	<-- Use designated initializer of superclass
	let c2 = AlphaColor()	<-- Error.
	
	println("(\(c1.red), \(c1.green), \(c1.blue), \(c1.alpha))")	<-- (255.0, 128.0, 255.0, 255.0)
	```

* No initializer defined in subclass and use _Convenience_ one of superclass

	```
	class Color {
	    let red = 0.0, green = 0.0, blue = 0.0
	    
	    init(red: Double, green: Double, blue: Double) {
	        self.red = red
	        self.green = green
	        self.blue = blue
	    }
	    
	    convenience init() {
	        self.init(red: 128.0, green: 128.0, blue: 128.0)
	        
	    }
	
	}
	
	class AlphaColor : Color {
	    let alpha = 255.0
	}
	
	let c1 = AlphaColor()	<-- Use Convenience initializer of superclass
	
	println("(\(c1.red), \(c1.green), \(c1.blue), \(c1.alpha))")	<-- (128.0, 128.0, 128.0, 255.0)
	```

* Subclass __MUST__ use _Designated_ initializer of superclass in its initializers.

	```
	class Color {
	    let red = 0.0, green = 0.0, blue = 0.0
	    
	    init(red: Double, green: Double, blue: Double) {
	        self.red = red
	        self.green = green
	        self.blue = blue
	    }
	    
	    convenience init() {
	        self.init(red: 128.0, green: 128.0, blue: 128.0)
	        
	    }
	
	}
	
	class AlphaColor : Color {
	    let alpha = 255.0
	        
	    init(alpha: Double) {
	        super.init(red: 0.0, green: 0.0, blue: 0.0)
	        self.alpha = alpha
	    }
	}
	
	let c1 = AlphaColor(alpha: 128.0)
	
	println("(\(c1.red), \(c1.green), \(c1.blue), \(c1.alpha))")	<-- (0.0, 0.0, 0.0, 128.0)
	```

	```
	class Color {
	    let red = 0.0
	    var green = 0.0, blue = 0.0
	    
	    init(red: Double, green: Double, blue: Double) {
	        self.red = red
	        self.green = green
	        self.blue = blue
	    }
	    
	    convenience init() {
	        self.init(red: 128.0, green: 128.0, blue: 128.0)
	        
	    }
	    
	}
	
	class AlphaColor : Color {
	    let alpha = 255.0
	    
	    init(alpha: Double) {
	        super.init()			<-- Error, invoke convience initializers of superclass
	        self.alpha = alpha
	    }
	}
	
	```
	
	```
	class Color {
	    let red = 0.0
	    var green = 0.0, blue = 0.0
	    
	    init(red: Double, green: Double, blue: Double) {
	        self.red = red
	        self.green = green
	        self.blue = blue
	    }
	    
	    convenience init() {
	        self.init(red: 128.0, green: 128.0, blue: 128.0)
	        
	    }
	    
	}
	
	class AlphaColor : Color {
	    let alpha = 255.0
	    
	    init(alpha: Double) {
	        super.init(red: 0.0, green: 0.0, blue: 0.0)
	        self.alpha = alpha
	    }
	    
	    convenience init() {
	        super.init()	<-- Error, Must invoke designdated initializers of superclass
	    }
	}
	
	```


