# Swift Language Guide - Basic Operators

## Assignment

* Assignment __NOT__ return a value

	```
	if x = y { <-- Error
	}
	```

* Assignment with Tuple and Swap

	```
	var (x, y) = (1, 2)
	println("(x, y) = (\(x), \(y))")
	
	(x, y) = (y, x)
	println("(x, y) = (\(x), \(y))")
	```

## Remainder

__Support remainder (%) on FLOATING-POINT__

```
println(8 % 3.3)
```

## Ternary

```
question ? answer1 : answer2
```

## Range

* Closed Range

	```
	for i in 0...5 {
	    println(i)		<-- print 0 ~ 5
	}
	```

* Half-Closed Range

	```
	for i in 0..<5 {
	    println(i)
	}
	```

__P.S. Half-Closed range operator is changed. old `..` is NOT USED.__

