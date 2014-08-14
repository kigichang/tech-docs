# Swift Language Guide - Value and Reference Type

## Value Type

* Integer, Floating-point, Boolean (`Bool`)
* Structure (`struct`)
* Enumerations (`enum`)
* __String__
* __Array__
* __Dictionary__


## Reference Type

* Class (`class`)
* Function Type (`(parameter) -> (return type)`)
* Closure (`{ (parameter) -> (return type) in statements }`)


## Behavior

### Value Type 

Value type is that __Value__ is copied when it is assigned to a variable or constant, or when it is passed to a function. Therefore, we have a new __Value__ (__Instance__) in memory.

Value type is __Pass by Value__ when passed to functions.

* __Sample about String__

	```
	var str = "Hello, World"
	var str2 = str				<-- Copy a New Value from str and assign to str2
	
	println("str = \(str)")		<-- str = Hello, World
	println("str2 = \(str2)")	<-- str2 = Hello, World
	
	str.write(". abc")
	
	println("str = \(str)")		<-- str = Hello, World. abc
	println("str2 = \(str2)")	<-- str2 = Hello, World
	```

* __Sample about Structure__

	```
	struct Point : Printable {
	    var x = 0, y = 0
	    
	    var description : String {
	        return "(\(x), \(y))"
	    }
	}
	
	var point1 = Point(x: 10, y: 20)
	var point2 = point1				<-- Copy a New Value from point1 and assign to point2
	
	println("point1 = \(point1)")	<-- point1 = (10, 20)
	println("point2 = \(point2)")	<-- point2 = (10, 20)
	
	point1.x = 100
	
	println("point1 = \(point1)")	<-- point1 = (100, 20)
	println("point2 = \(point2)")	<-- point2 = (10, 20)
	```


### Reference Type

Reference type is that __Reference__ to the same instance is copied when it is assigned to a variable or constant, or when it is passed to a function. Therefore, we do __NOT__ have new instance in memory.

Reference type is __Pass by  Value__ when passed to functions.

* __Sample about Class__

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
	}
	
	
	var point1 = Point(x: 10, y: 20)
	var point2 = point1
	
	println("point1 = \(point1)")	<-- point1 = (10, 20)
	println("point2 = \(point2)")	<-- point2 = (10, 20)
	
	point1.x = 100
	
	println("point1 = \(point1)")	<-- point1 = (100, 20)
	println("point2 = \(point2)")	<-- point2 = (100, 20)
	
	func movePoint(point: Point) {
	    point.y = 200
	}
	
	movePoint(point1)	<-- Copy a reference value from point1, so movePoint change y value of point1
	
	println("point1 = \(point1)")	<-- point1 = (100, 200)
	println("point2 = \(point2)")	<-- point2 = (100, 200)
	
	
	```

	
__Summary: Variable is _Passed by Reference_ with `inout`.__

* __Pass By Value__

	```
	struct Point : Printable {
	    var x = 0, y = 0
	    var description : String {
	        return "(\(x), \(y))"
	    }
	}
	
	class Color : Printable {
	    var red = 0.0, green = 0.0, blue = 0.0
	    
	    var description : String {
	        return "(\(red), \(green), \(blue))"
	    }
	}
	
	var point1 = Point(x: 0, y: 0)
	var point2 = point1
	
	var color1 = Color()
	var color2 = color1
	
	func testByValue(var point: Point, var color: Color) {
	    point.x = 100
	    color = Color()
	    color.green = 100.0
	    println(color === color1)	<-- false
	    println(color === color2)	<-- false
	}
	
	println("point1 = \(point1)")	<-- point1 = (0, 0)
	println("point2 = \(point2)")	<-- point2 = (0, 0)
	
	println("color1 = \(color1)")	<-- color1 = (0.0, 0.0, 0.0)
	println("color2 = \(color2)")	<-- color2 = (0.0, 0.0, 0.0)
	
	testByValue(point1, color1)
	
	println("point1 = \(point1)")	<-- point1 = (0, 0)
	println("point2 = \(point2)")	<-- point2 = (0, 0)
	
	println("color1 = \(color1)")	<-- color1 = (0.0, 0.0, 0.0)
	println("color2 = \(color2)")	<-- color2 = (0.0, 0.0, 0.0)
	```

	Swift copy the reference value of color1 and assign to the parameter `color` when invoking `testByValue(point1, color1)`. We change the reference value of `color` in function `testByValue` and it does __NOT__ affect `color1`. Because `color1` and `color` have their own copy, `color === color1` and `color === color2` are __ALL FALSE__.

* __Pass by Reference__

	```
	struct Point : Printable {
	    var x = 0, y = 0
	    var description : String {
	        return "(\(x), \(y))"
	    }
	}
	
	class Color : Printable {
	    var red = 0.0, green = 0.0, blue = 0.0
	    var description : String {
	        return "(\(red), \(green), \(blue))"
	    }
	}
	
	var point1 = Point(x: 0, y: 0)
	var point2 = point1
	
	var color1 = Color()
	var color2 = color1
	
	println("point1 = \(point1)")	<-- point1 = (0, 0)
	println("point2 = \(point2)")	<-- point2 = (0, 0)
	println("color1 = \(color1)")	<-- color1 = (0.0, 0.0, 0.0)
	println("color2 = \(color2)")	<-- color2 = (0.0, 0.0, 0.0)
	println(color1 === color2)		<-- true
	
	
	func testByReference(inout point: Point, inout color: Color) {
	    point.x = 100
	    
	    color = Color()
	    color.green = 100.0
	    
	    println(color === color1)	<-- true
	    println(color1 === color2)	<-- false
	}
	
	testByReference(&point1, &color1)	<-- Pass by Reference
	
	println("point1 = \(point1)")	<-- point1 = (100, 0)
	println("point2 = \(point2)")	<-- point2 = (0, 0)
	println("color1 = \(color1)")	<-- color1 = (0.0, 100.0, 0.0)
	println("color2 = \(color2)")	<-- color2 = (0.0, 0.0, 0.0)
	
	println(color1 === color2)		<-- false
	```

	Passing by Reference with `inout` when declaring a function and `&` when invoking a function will change the value of original variable, even the reference value. `color1` is passed by reference when invoking `testByReference`. We assign New Instance to `color` and the referece value of `color1` is also changed because of passing by reference. Therefore, `color === color1` is true and `color1 === color2)` is false.


__p.s. `===` is AnyObject Identity Operator. It is `true` when variables refer to the SAME instance.__


* __Arrays__

	Array is _Value Type_. So, _Value_ is _Copied_ when it is assigned to variable or constant, or when passed to functions. 

	* The element in Array is _Value Type_, the _Value_ is copied to store in Array.
	* The element in Array is _Reference Type_, the _Reference Value_ is copied to store in Array.

	__Element Behavior in Array__
	
	* Value Type Elements in Array

		```
		struct Point : Printable {
		    var x = 0, y = 0
		    
		    var description : String {
		        return "(\(x), \(y))"
		    }
		}
		
		var points1 = [Point(x: 1, y: 1), Point(x: 2, y: 2), Point(x: 3, y: 3)]
		var points2 = points1	<-- copy point1 value and assign to points2, and values of elements are copied as well.
		
		println("point1 = \(points1[1])")	<-- point1 = (2, 2)
		println("point2 = \(points2[1])")	<-- point2 = (2, 2)
		
		points1[1].x = 12
		println("point1 = \(points1[1])")	<-- point1 = (12, 2)
		println("point2 = \(points2[1])")	<-- point2 = (2, 2)
		
		points1[1] = Point(x: 22, y: 22)
		println("point1 = \(points1[1])")	<-- point1 = (22, 22)
		println("point2 = \(points2[1])")	<-- point2 = (2, 2)
		
		points1.append(Point(x: 4, y: 4))
		println(points1.count)	<-- 4
		println(points2.count)	<-- 3
		```
		
	* Reference Type Elements in Array

		```
		class Color : Printable {
		    var red = 0.0, green = 0.0, blue = 0.0
		    init() { }
		    init(red: Double, green: Double, blue: Double) {
		        self.red = red
		        self.green = green
		        self.blue = blue
		    }
		    
		    var description : String {
		        return "(\(red), \(green), \(blue))"
		    }
		    
		}
		
		var colors1 = [Color(red: 255.0, green: 0.0, blue: 0.0), Color(red: 0.0, green: 255.0, blue: 0.0), Color(red: 0.0, green: 0.0, blue: 255.0)]
		var colors2 = colors1	<-- copy color1 value and assign to color2, and the reference values of elements are copied as well.
		
		println("colors1 = \(colors1[1])")		<-- colors1 = (0.0, 255.0, 0.0)
		println("colors2 = \(colors2[1])")		<-- colors2 = (0.0, 255.0, 0.0)
		
		colors1[1].green = 128.0
		println("colors1 = \(colors1[1])")		<-- colors1 = (0.0, 128.0, 0.0)
		println("colors2 = \(colors2[1])")		<-- colors2 = (0.0, 128.0, 0.0)
		
		colors1[1] = Color(red: 128.0, green: 128.0, blue: 128.0)
		println("colors1 = \(colors1[1])")		<-- colors1 = (128.0, 128.0, 128.0)
		println("colors2 = \(colors2[1])")		<-- colors2 = (0.0, 128.0, 0.0)
		
		colors1.append(Color(red: 255.0, green: 255.0, blue: 255.0))
		println(colors1.count)		<-- 4
		println(colors2.count)		<-- 3
		
		```

__Note: The behavior of Array is NOT the same as _The Swift Programming Language_. I use XCode beta 5. __


* __Dictionary__

	Dictionary is the __SAME__ as Array. 

	__Value Behavior in Dictionary__
	
	* Value Type Value in Dictionary

		```
		struct Point : Printable {
		    var x = 0, y = 0
		    
		    var description: String {
		        return "(\(self.x), \(self.y))"
		    }
		    
		}
		
		class Color : Printable {
		    var red = 0.0, green = 0.0, blue = 0.0
		    init() { }
		    init(red: Double, green: Double, blue: Double) {
		        self.red = red
		        self.green = green
		        self.blue = blue
		    }
		    
		    var description: String {
		        return "(\(red), \(green), \(blue))"
		    }
		}
		
		func dumpDict<T, U>(dict: [T:U]) {
		    for (key, value) in dict {
		        print("(\(key), \(value)) ")
		    }
		    println()
		}
		
		var dict1 = ["zero" : Point(x: 0, y: 0), "one" : Point(x: 1, y: 1)]
		var dict2 = dict1
		
		dumpDict(dict1)		<-- (one, (1, 1)) (zero, (0, 0)) 
		dumpDict(dict2)		<-- (one, (1, 1)) (zero, (0, 0))
		
		dict1["zero"] = Point(x: 0, y: -1)
		
		dumpDict(dict1)		<-- (one, (1, 1)) (zero, (0, -1))
		dumpDict(dict2)		<-- (one, (1, 1)) (zero, (0, 0))
		
		var point = dict1["one"]!	<-- Copy a new Value for assignment
		point.x = 200				<-- Change x value of New Copy
		
		dumpDict(dict1)				<-- (one, (1, 1)) (zero, (0, -1)) (No Effect)
		dumpDict(dict2)				<-- (one, (1, 1)) (zero, (0, 0)) 
		
		```

	* Reference Type Value in Dictionary

		```
		struct Point : Printable {
		    var x = 0, y = 0
		    
		    var description: String {
		        return "(\(self.x), \(self.y))"
		    }
		    
		}
		
		class Color : Printable {
		    var red = 0.0, green = 0.0, blue = 0.0
		    init() { }
		    init(red: Double, green: Double, blue: Double) {
		        self.red = red
		        self.green = green
		        self.blue = blue
		    }
		    
		    var description: String {
		        return "(\(red), \(green), \(blue))"
		    }
		}
		
		func dumpDict<T, U>(dict: [T:U]) {
		    for (key, value) in dict {
		        print("(\(key), \(value)) ")
		    }
		    println()
		}
			
		var dict1 = ["white": Color(red: 255.0, green: 255.0, blue: 255.0)]
		var dict2 = dict1
		
		dumpDict(dict1)		<-- (white, (255.0, 255.0, 255.0))
		dumpDict(dict2)		<-- (white, (255.0, 255.0, 255.0))
		
		var color = dict1["white"]!		<-- Copy Reference Value for assignment
		color.red = 128.0				<-- change red value of the referred Instance
		
		dumpDict(dict1)					<-- (white, (128.0, 255.0, 255.0)) (red value changed)
		dumpDict(dict2)					<-- (white, (128.0, 255.0, 255.0)) (red value changed)
		
		dict1["white"] = Color(red: 128.0, green: 128.0, blue: 128.0)	<-- Copy a the Reference Value of the New Color to dict1["white"]
		
		dumpDict(dict1)		<-- (white, (128.0, 128.0, 128.0)) <-- the new color value
		dumpDict(dict2)		<-- (white, (128.0, 255.0, 255.0)) <-- keep the old reference value
		```



