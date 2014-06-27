# 用途
Enumeration 通常用在所謂的狀態值或者屬性，這些值的特性通常都是由多個固定值組成，且不允許工程師任意自行添加新的值。

Ex: Database 有個欄位 `is_valid` ，設定只能是 `0` 或 `1`，在程式的寫作過程，就不允許工程師使用或修改成其他值(ex: `2`)，以免造成資料庫資料的錯亂。

要避免上述的情況發生，就是限制工程師，也因此就有了 `Enumeration`。


# 最簡單的 Enumeration

## Java 版

* Source

	```
	public enum DirectionJava {
	  TOP, DOWN, LEFT, RIGHT;
	}
	
	```
	
* `javap -p`

	```
	Compiled from "DirectionJava.java"
	public final class DirectionJava extends java.lang.Enum<DirectionJava> {
	  public static final DirectionJava TOP;
	  public static final DirectionJava DOWN;
	  public static final DirectionJava LEFT;
	  public static final DirectionJava RIGHT;
	  private static final DirectionJava[] $VALUES;
	  public static DirectionJava[] values();
	  public static DirectionJava valueOf(java.lang.String);
	  private DirectionJava();
	  static {};
	}
	```
	
在 Java 使用 `enum` 這個 keyword 來宣告即可。從 Java Bytecode 可以發現，其實 `enum` 底層本身是個 `final class`, 也因此 `enum` 不能再繼承其他的 class (Java 是單一繼承)。 Constructor 設計成 __private__，所以工程師不能隨意自行產生值，只能使用內定的值或 `null`。


## Scala 版本

* Source 

	```
	object DirectionScala extends Enumeration {
	  val Top, Down, Left, Right = Value
	}
	```

* `scalap -private`

	```
	object DirectionScala extends scala.Enumeration {
	  def this() = { /* compiled code */ }
	  val Top : scala.Enumeration.Value = { /* compiled code */ }
	  val Down : scala.Enumeration.Value = { /* compiled code */ }
	  val Left : scala.Enumeration.Value = { /* compiled code */ }
	  val Right : scala.Enumeration.Value = { /* compiled code */ }
	}
	```
	
* `javap -p DirectionScala`

	```
	Compiled from "DirectionScala.scala"
	public final class DirectionScala {
	  public static scala.Enumeration$Value Right();
	  public static scala.Enumeration$Value Left();
	  public static scala.Enumeration$Value Down();
	  public static scala.Enumeration$Value Top();
	  public static scala.Enumeration$Value withName(java.lang.String);
	  public static scala.Enumeration$Value apply(int);
	  public static int maxId();
	  public static scala.Enumeration$ValueSet values();
	  public static java.lang.String toString();
	}
	```
* `javap -p DirectionScala\$.class`

	```
	public static final DirectionScala$ MODULE$;
	  private final scala.Enumeration$Value Top;
	  private final scala.Enumeration$Value Down;
	  private final scala.Enumeration$Value Left;
	  private final scala.Enumeration$Value Right;
	  public static {};
	  public scala.Enumeration$Value Top();
	  public scala.Enumeration$Value Down();
	  public scala.Enumeration$Value Left();
	  public scala.Enumeration$Value Right();
	  private DirectionScala$();
	```

Scala 的 [`Enumeration`](https://github.com/scala/scala/blob/v2.11.1/src/library/scala/Enumeration.scala#L1) 跟 Java 的很類似，但 Scala 的內定值型別是 Enumeration.Values, 它本身是個 `abstract class` 在 `Enumeration` 內有一個實作的 class: `protected class Val`，如果需要自定 Enumeration 時，就會需要繼承這個 class。


# 進階版

使用 Java 說明文件上的 [Planet 範例](http://docs.oracle.com/javase/tutorial/java/javaOO/enum.html)。

##  Java 版

```
public enum Planet {
    MERCURY (3.303e+23, 2.4397e6),
    VENUS   (4.869e+24, 6.0518e6),
    EARTH   (5.976e+24, 6.37814e6),
    MARS    (6.421e+23, 3.3972e6),
    JUPITER (1.9e+27,   7.1492e7),
    SATURN  (5.688e+26, 6.0268e7),
    URANUS  (8.686e+25, 2.5559e7),
    NEPTUNE (1.024e+26, 2.4746e7);

    private final double mass;   // in kilograms
    private final double radius; // in meters
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }
    private double mass() { return mass; }
    private double radius() { return radius; }

    // universal gravitational constant  (m3 kg-1 s-2)
    public static final double G = 6.67300E-11;

    double surfaceGravity() {
        return G * mass / (radius * radius);
    }
    double surfaceWeight(double otherMass) {
        return otherMass * surfaceGravity();
    }
    public static void main(String[] args) {
        if (args.length != 1) {
            System.err.println("Usage: java Planet <earth_weight>");
            System.exit(-1);
        }
        double earthWeight = Double.parseDouble(args[0]);
        double mass = earthWeight/EARTH.surfaceGravity();
        for (Planet p : Planet.values())
           System.out.printf("Your weight on %s is %f%n",
                             p, p.surfaceWeight(mass));
    }
}
```

## Scala 版
Scala 實作的方式很多，網路上都找得到，以下是我自己寫的版本。

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

重點在繼承 `Enumeration.Val`，由於 Scala 的 values 沒有 generic 的功能，所以需要做轉型，不過 Enumeration 的 values 功能很少用，所以不用太在意。

