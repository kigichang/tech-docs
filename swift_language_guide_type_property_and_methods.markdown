# Swift Language Guide - Type Properties and Methods


* Type properties and methods are like `static` in Java.
* Use `static` in Value Type and `class` in Reference Type
* Type computed property and methods can __NOT__ use instance member data.

## Type Properties

* __Stored__ and __Computed__ property belong to __Value__ type (Structure and Enumerations)
* __Reference__ Type (Class) only has __Computed__ property
* __Stored__ Type Property __MUST__ have initial value.


## Syntax

* Enumerations

	```
	enum TestEnum {
	    static var staticX = 0
	    
	    static var staticY : Int {
	        get {
	        return staticX + 10
	        }
	        set {
	            staticX = newValue - 10
	        }
	    }
	}
	
	println(TestEnum.staticX)
	println(TestEnum.staticY)
	TestEnum.staticY = 30
	println(TestEnum.staticX)
	```

* Structure

	```
	struct TestStruct {
	    static var staticX = 0
	    
	    static var staticY : Int {
	        get {
	        return staticX + 10
	        }
	        set {
	            staticX = newValue - 10
	        }
	    }
	}
	
	println(TestStruct.staticX)
	println(TestStruct.staticY)
	TestStruct.staticY = 50
	println(TestStruct.staticX)
	
	```

* Class

	```
	var globalX = 0
	class TestClass {
	    class var classX : Int {
	        get {
	            return globalX + 10
	        }
	        set {
	            globalX = newValue - 10
	        }
	    }
	}
	
	println(TestClass.classX)
	TestClass.classX = 90
	println(TestClass.classX)
	println(globalX)
	
	```


## Type Methods

* Do __NOT__ need 'mutating` when type method of value type changing type property.

* Enumerations

	```
	enum TestEnum {
	    static var staticX = 0
	    
	    static var staticY : Int {
	        get {
	        return staticX + 10
	        }
	        set {
	            staticX = newValue - 10
	        }
	    }
	    
	    static func addStaticY(y: Int) {
	        staticY = y
	    }
	}
	
	println(TestEnum.staticX)
	println(TestEnum.staticY)
	TestEnum.addStaticY(30)
	println(TestEnum.staticX)
	```

* Structure

	```
	struct TestStruct {
	    static var staticX = 0
	    
	    static var staticY : Int {
	        get {
	        return staticX + 10
	        }
	        set {
	            staticX = newValue - 10
	        }
	    }
	    
	    static func addStaticY(y: Int) {
	        staticY = y
	    }
	}
	
	println(TestStruct.staticX)
	println(TestStruct.staticY)
	TestStruct.addStaticY(50)
	println(TestStruct.staticX)
	
	```

* Class

	```
	var globalX = 0
	class TestClass {
	    class var classX : Int {
	        get {
	            return globalX + 10
	        }
	        set {
	            globalX = newValue - 10
	        }
	    }
	    
	    class func addClassX(x: Int) {
	        classX = x
	    }
	}
	
	println(TestClass.classX)
	TestClass.addClassX(90)
	println(TestClass.classX)
	println(globalX)
	
	```
	
