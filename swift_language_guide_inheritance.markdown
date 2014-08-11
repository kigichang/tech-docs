# Swift Language Guide - Inheritance

__ONLY__ _Reference Type_ has Inheritance.

## Initializers

* Subclass initializers are __NOT__ inherited by default. So, subclass __MUST__ invoke _Designated_ initializers of superclass in its initializers.

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
	        super.init()			<-- Error, invoke convenience initializers of superclass
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
	        super.init()	<-- Error, Must invoke designated initializers of superclass
	    }
	}
	
	```
	
* Subclass can __NOT__ modify _Constant_ properties of superclass.

	```
	class Color {
	    let red = 0.0	<-- Constant
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
	        self.green = 128.0	
	        self.red = 255.0		<-- Error, Can not modify constant properties of superclass
	    }
	}
	```
	
## Override

Use `override` to override methods, properties, and _Subscripts_

* Subclass can __NOT__ override _Stored_ properties of superclass _Directly_.

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
	    override var green = 128.0	<-- Error, can not override stored properties of superclass.
	    
	    init(alpha: Double) {
	        super.init(red: 0.0, green: 0.0, blue: 0.0)
	        self.alpha = alpha
	        self.green = 128.0
	    }
	    
	    
	}
	```

* Subclass __CAN__ override _Stored_ properties of superclass with _Computed_ ones.

	```
	class Color {
	    let red = 0.0, green = 0.0, blue = 0.0
	    var count = 0			<-- a stored property
	    
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
	    
	    override var count : Int {		<-- override count with computed property
	        get {
	            return super.count
	        }
	        
	        set {
	            super.count = newValue
	        }
	    }
	    
	}
	
	let c1 = AlphaColor(alpha: 128.0)
	
	println(c1)
	```



* Subclass __CAN__ override _Computed_ properties of superclass.

	```
	class Color : Printable {
	    let red = 0.0, green = 0.0, blue = 0.0
	    
	    init(red: Double, green: Double, blue: Double) {
	        self.red = red
	        self.green = green
	        self.blue = blue
	    }
	    
	    convenience init() {
	        self.init(red: 128.0, green: 128.0, blue: 128.0)
	        
	    }
	    
	    var description : String {
	        return "(\(red), \(green), \(blue))"
	    }
	    
	}
	
	class AlphaColor : Color {
	    let alpha = 255.0
	    
	    init(alpha: Double) {
	        super.init(red: 0.0, green: 0.0, blue: 0.0)
	        self.alpha = alpha
	    }
	    
	    override var description : String {	<-- Override computed properties.
	        return "(\(red), \(green), \(blue), \(alpha))"
	    }
	}
	
	let c1 = AlphaColor(alpha: 128.0)
	
	println(c1)
	```

* Subclass can __Add__ Property Observers to _Stored_ and _Computed_ properties with __Override__ (`override`)

	```
	class Color {
	    let red = 0.0, green = 0.0, blue = 0.0
	    var count = 0			<-- Stored Property
	    
	    var count2 : Int {		<-- Computed Property
	        get {
	            println("count2 getter")
	            return self.count
	        }
	        
	        set {
	            println("count2 setter")
	            count = newValue
	        }
	    }
	    
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
	    var cc : Int = 0
	    
	    init(alpha: Double) {
	        super.init(red: 0.0, green: 0.0, blue: 0.0)
	        self.alpha = alpha
	    }
	    
	    override var count2 : Int {			<-- Add property observer to computed property
	        didSet {
	            cc += 1
	            println("didset: cc = \(cc)")
	        }
	        
	        willSet {
	            cc += 2
	            println("willset: cc = \(cc)")
	        }
	    }
	    
	    override var count: Int {			<-- Add property observer to stored property
	        didSet {
	            println("didset: count = \(count)")
	        }
	        
	        willSet {
	            println("willset: count = \(count)")
	        }
	    }
	    
	    
	}
	
	let c1 = AlphaColor(alpha: 128.0)
	println("before setter")
	c1.count2 = 20
	println("after setter")
	println(c1.count)
	println(c1.cc)
	```
	
### Preventing Overrides

Use `final` to prevent overrides, even adding property observers.

```
class Color {
    let red = 0.0, green = 0.0, blue = 0.0
    final var count = 0			<-- Final Stored Property
    
    final var count2 : Int {	<-- Final Computed Property
        get {
            println("count2 getter")
            return self.count
        }
        
        set {
            println("count2 setter")
            count = newValue
        }
    }
    
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
    var cc : Int = 0
    
    init(alpha: Double) {
        super.init(red: 0.0, green: 0.0, blue: 0.0)
        self.alpha = alpha
    }
    
    override var count2 : Int {		<-- Error, can NOT override
        didSet {
            cc += 1
            println("didset: cc = \(cc)")
        }
        
        willSet {
            cc += 2
            println("willset: cc = \(cc)")
        }
    }
    
    override var count: Int {		<-- Error, can NOT override
        didSet {
            println("didset: count = \(count)")
        }
        
        willSet {
            println("willset: count = \(count)")
        }
    }
    
    
}
```

