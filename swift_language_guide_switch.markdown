# Swift Language Guide - SWITCH

## case MUST have body

```
let c = 20

switch c {
case 1:		<-- Error
case 20:
    println("20")
}
```


## usage

```
let a = (1, 2)

switch a {
case let (x, y) where x == y:
    println("x = y")
    
case (let x, _) where x >= 0:
    println("x >= 0")
case (0, let y):
    println("x is zero")
    
case (_, 0):
    println("y is zero")
    
case (0...5, 1..<5):
    println("0 <= x <= 5 and 1<= y < 5")
    
default:
    println("nothing")
}
```

## BREAK

`break` is not needed in switch-case, but you can use it if you want to do nothing.

```
let c = 20

switch c {
case 1:
    break
case 20:
    println("20")
default:
    break
}
```

## FALLTHROUTH

`fallthrough` dose __NOT__ check __NEXT__ case condition and __EXECUTE__ it's body.

```
let b = 10

switch b {
case 1...20:
    println("1 <= b <= 20")			<-- match and println
    fallthrough
    
case 11...20:
    println("11 <= b <= 20")		<-- println because of fallthrough
    
case 5...15:
    println("1 <= b <= 15")			<-- NOT be applied because of implicit break in case 11...20
    
default:
    println("nothing")				<-- NOT be applied because of implicit break in case 11...20
}
```

`b = 10` matches `case 1...20`, fall in `case 11...200` and implicit `break` after `println("11 <= b <= 20")`. So, `case 5...15` and `default` will not be applied.

### Limitation

`fallthrough` can __NOT__ transfer control to a case label declares variables.

```
case (0...5, 1..<5):
    println("0 <= x <= 5 and 1<= y < 5")
    fallthrough		<-- Error
    
case (0, let y):	<-- value binding
    println("x is zero")
```