Scala 的 Access Level 觀念和 Java 差不多，但用法很大的不同，而且 Scala 給予的彈性比 Java 來得高，也因此更方便設計系統。


## Java Access Level 
[資料來源](http://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html)

Java 沒有宣告時(no explicit modifier)，是 __package-private__, 並非 __public__ 這點要特別注意的。__package-private__是指可以在同 package 被存取，但 subclass 卻不行。

由 [Java 說明文件](http://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html)的表格，可以了解什麼是 __package_private__


Modifier | Class | Package | Subclass | World
:------: | :---: | :-----: | :------: | :---:
public | Y | Y | Y | Y 
protected | Y | Y | Y | N
no modifier <br /> (__package-private__) | Y | Y | N | N
private | Y | N | N | N

![實例](http://docs.oracle.com/javase/tutorial/figures/java/classes-access.gif "實例")

Modifier | Alpha | Beta | AlphaSub | Gamma
:------: | :---: | :--: | :------: | :---:
public | Y | Y | Y | Y
protected | Y | Y | Y | N
no modifier <br /> (__package-private__) | Y | Y | N | N
private | Y | N | N | N


## Scala Access Level 基本
仿造 Java 文件的上範例 

```
package one {
  
  class Alpha {
    val a = 1
    protected val b = 2
    private val c = 3
    
    def func() {
      a
      b
      c
    }
  }
 
  
  class Beta {
    def test() {
      val alpha = new Alpha()
      
      alpha.a
      alpha.b // error: value b in class Alpha cannot be accessed in one.Alpha Access to protected value b not permitted because enclosing class Beta in package one is not a subclass of class Alpha in package one where target is defined
      alpha.c // error: value c in class Alpha cannot be accessed in one.Alpha
      
    }
  }
}

package two {
  
  class AlphaSub extends one.Alpha {
    
    def test() {
      this.a
      this.b
      this.c // error: value c in class Alpha cannot be accessed in two.AlphaSub
    }
  }
  
  class Gamma {
    def test() {
      val alpha = new one.Alpha
      
      alpha.a
      alpha.b // error: value b in class Alpha cannot be accessed in one.Alpha Access to protected value b not permitted because enclosing class Gamma in package two is not a subclass of class Alpha in package one where target is defined
      alpha.c // error: value c in class Alpha cannot be accessed in one.Alpha
      
    }
    
  }
  
}
```

由上面的範例可以整理出

Modifier | Alpha | Beta | AlphaSub | Gamma
:------: | :---: | :--: | :------: | :---:
no modifier <br /> (__public__) | Y | Y | Y | Y
protected | Y | N | Y | N
private | Y | N | N | N

Scala 沒有宣告的話，則是 __public__，與 Java (package-private) 不同。 __protected__ 在 Scala 也與 Java 不同，在 Java 中，__protected__ 可以被同 __package__ 存取，但 Scala 不行。

Modifier | Scala | Java
:------: | :---: | :--:
no modifier | public | package-private
protected | 自己, subclass | 自己, subclass, 同 package


## Scala Access Level 進階
Scala 有比 Java 更彈性的 Access control，在 Scala 中，可以宣告 `modifier[package or class]` 的方式，ex: `private[one]`，來實作不同等級的 access control。

### 實作 Java Package-Private
修改上面的範例，在 `Alpha` 中新加 `private[one] val d = 4` 這個變數。這個變數的存取等級就同 Java 的 package-private，在同 package 可以被存取，但 subclass 卻不行。

ex:

```
package one {
  
  class Alpha {
    val a = 1
    protected val b = 2
    private val c = 3
    
    private[one] val d = 4 // package-private
    
    def func() {
      a
      b
      c
      d
    }
  }
 
  
  class Beta {
    def test() {
      val alpha = new Alpha()
      
      alpha.a
      alpha.b // error: value b in class Alpha cannot be accessed in one.Alpha Access to protected value b not permitted because enclosing class Beta in package one is not a subclass of class Alpha in package one where target is defined
      alpha.c // error: value c in class Alpha cannot be accessed in one.Alpha
      
      alpha.d
    }
  }
}

package two {
  
  class AlphaSub extends one.Alpha {
    
    def test() {
      this.a
      this.b
      this.c // error: value c in class Alpha cannot be accessed in two.AlphaSub
      this.d // error: value d in class Alpha cannot be accessed in two.AlphaSub
    }
  }
  
  class Gamma {
    def test() {
      val alpha = new one.Alpha
      
      alpha.a
      alpha.b // error: value b in class Alpha cannot be accessed in one.Alpha Access to protected value b not permitted because enclosing class Gamma in package two is not a subclass of class Alpha in package one where target is defined
      alpha.c // error: value c in class Alpha cannot be accessed in one.Alpha
      alpha.d // error: value d in class Alpha cannot be accessed in one.Alpha
    }
    
  }
  
}
```

### 授與 Private Constructor 使用權
Java 的 private Constructor 常用在 Singleton，除了自身外，外界都無法使用。但 Scala 卻可以授與這項權力給指定的 package 或 class。

以下是我自己寫的 Enumeration. 其中的 `final case class Planet private[Planets] (mass: Double, radius: Double) extends Val` 就是將 `Planet` 的 private constructor 授與 `Planets`

```
object Planets extends Enumeration {
  
  val G: Double = 6.67300E-11

  final case class Planet private[Planets] (mass: Double, radius: Double) extends Val {
    def surfaceGravity(): Double = G * mass / (radius * radius)
    def surfaceWeight(otherMass: Double): Double = otherMass * surfaceGravity()
  }
  
  val Mercury = Planet(3.303e+23, 2.4397e6)
  val Venus = Planet(4.869e+24, 6.0518e6)
  val Earth = Planet(5.976e+24, 6.37814e6)
  val Mars = Planet(6.421e+23, 3.3972e6)
  val Jupiter = Planet(1.9e+27,   7.1492e7)
  val Saturn = Planet(5.688e+26, 6.0268e7)
  val Uranus = Planet(8.686e+25, 2.5559e7)
  val Neptune = Planet(1.024e+26, 2.4746e7)
  
  
  def main(args: Array[String]) {
    require(args.length == 1, "Usage: java Planet <earth_weight>")
    
    val earthWeight = args(0).toDouble  
    val mass = earthWeight / Planets.Earth.surfaceGravity
    
    Planets.values.foreach(p => println("Your weight on %s is %f%n".format(p, p.asInstanceOf[Planet].surfaceWeight(mass))))
  }
  
}
```

### 限 Instance 存取
在 Scala 中，可以利用 `private[this]` 來限定只能是同 instance 才能存取，這項限制比 `private` 更加嚴格，這也許是 Scala 標榜平行運算下的產物。

ex:

```
class TestPrivateThis {
  private[this] val a = 10
  
  def func(): Int = a + 10
  
  def func(that: TestPrivateThis): Int = a + that.a // error: value a is not a member of TestPrivateThis
}
```

## 總結

Scala 在設計上，比較符合一般印象中的 access level，No modifier = public. protected 只能自己與 subclass 存取；且 Scala 比 Java 更為彈性。