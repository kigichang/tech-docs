# Swift Language Guide - Subscripts

Swift subscripts is like magic methods `__get($name)` and `__set($name, $value)` in PHP.


## Syntax

* Subscripts may have __MANY__
* use `[]` like Array or Dictionary

```
enum Axis : Int {
    case X = 0, Y, Z
}

struct Point : Printable {
    var location = [Double](count: 3, repeatedValue: 0.0)
    
    var description : String {
        return "(\(location[0]), \(location[1]), \(location[2]))"
    }
    
    subscript(axis: Axis) -> Double {
        get {
            return location[axis.toRaw()]
        }
        set {
            location[axis.toRaw()] = newValue
        }
    }
    
    subscript(x: Axis, y: Axis) -> (Double, Double) {
        get {
            return (location[x.toRaw()], location[y.toRaw()])
        }
        set {
            location[x.toRaw()] = newValue.0
            location[y.toRaw()] = newValue.1
        }
    }
    
    init() { }
    init(x: Double, y: Double, z: Double) {
        location[0] = x
        location[1] = y
        location[2] = z
    }
}


var point = Point(x: 1.0, y: 2.0, z: 3.0)
println("point = \(point)")
println("point[x] = \(point[.X])")
point[.Y] = 5.5
println("point[y] = \(point[.Y])")
println("point = \(point)")

var (x, y) = point[.X, .Y]
println("(\(x), \(y))")

point[.Y, .Z] = (x + 20.0, y - 10.0)
println("point = \(point)")


```
