# 前言
以下的內容，討論 Scala 與 Java 在功能與觀念上的不同，不會去比較語法上的不同。

# Import
* Scala 的 `import` 已有 Java 的 `static import` 效果
* Java 的 `import org.abc.*` 在 Scala 為 `import org.abc._` 不過在 Java 及 Scala 都不建議使用；還是詳細寫出 import 那些 class. 

# Val and Var
使用 `val` 宣告變數後，就不能再更改其 reference 值；而 `var` 是允許的，效果等同 Java 宣告。ex:

```
val a = "abc"
a = "def"
```
在 `a = "def"` 那一行，在 compiler 就會報錯。這項功能有個好處，可以確保變數的 reference 值不會被更動，讓系統的穩定度提高。對工程師在 coding，debug 也有很大的幫助


# Getter and Setter in Java Bean on Scala
Scala 在 compile 時，不同的宣告方式，會產生不同 Bean 的 getter 與 setter 的效果。可以使用 `scalap -private` 與 `javap -p` 來比較 Scala 做了那些事情。


## 比較 1: 最簡單的 class
	
```
class Rational(numer: Int, denom: Int) {

}

```

* `scalap -private Rational`

	```
	class Rational extends scala.AnyRef {
 	  def this(numer : scala.Int, denom : scala.Int) = { /* compiled code */ }
	}
	```
	
* `javap -p Rational`
	
	```
	public class Rational {
 		public Rational(int, int);
	}
	```

__最簡單的 class，沒有任何功能，也因此 Scala 的 compiler 不會自動產生 member data。__


## 比較 2: 加入 `val`

```
class Rational(val numer: Int, val denom: Int) {

}
```

* `scalap -private Rational`
	
	```
	class Rational extends scala.AnyRef {
 	  val numer : scala.Int = { /* compiled code */ }
	  val denom : scala.Int = { /* compiled code */ }
	  def this(numer : scala.Int, denom : scala.Int) = { /* compiled code */ }
	}
	```

* `javap -p Rational`

	```
	public class Rational {
		private final int numer;
		private final int denom;
		public int numer();
		public int denom();
		public Rational(int, int);
	}
	```

__很明顯的，加入 `val` 後， constructor 中的參數，會自動變成 private member data，也產生 getter 的函式：`numer()` 及 `denom()`。因為 `val` 是不可以修改 reference 值，所以 Scala 的 compiler 沒有幫忙產生 setter 的函式__


## 比較 3: 改用 `var`

```
class Rational(var numer: Int, var denom: Int) {

}
```

* `scalap -private Rational`
	
	```
	class Rational extends scala.AnyRef {
	  var numer : scala.Int = { /* compiled code */ }
	  var denom : scala.Int = { /* compiled code */ }
	  def this(numer : scala.Int, denom : scala.Int) = { /* compiled code */ }
	}
	```

* `javap -p Rational`

	```
	public class Rational {
		private int numer;
		private int denom;
		public int numer();
		public void numer_$eq(int);
		public int denom();
		public void denom_$eq(int);
		public Rational(int, int);
	}
	```
		

__由於 `var` 可以修改 reference，因此 Scala 的 compiler 就自動產生了 setter 的函式：`numer_$eq(int)` 及 `denom_$eq(int)`__


## Add `@BeanProperty`
Scala 產生的函式，對於給 Java 的使用者，雖然在寫 code 時，是可以使用的，但並不是那麼 friendly。這時候，可以使用 scala 內建的 annotation：`@BeanProperty`。ex:

```
import scala.reflect.BeanProperty

class Rational(@BeanProperty var numer: Int, @BeanProperty var denom: Int) {

}
```

* `scalap -private Rational`

	```
	class Rational extends scala.AnyRef {
 	  var numer : scala.Int = { /* compiled code */ }
	  def getNumer() : scala.Int = { /* compiled code */ }
	  def setNumer(x$1 : scala.Int) : scala.Unit = { /* compiled code */ }
	  var denom : scala.Int = { /* compiled code */ }
	  def getDenom() : scala.Int = { /* compiled code */ }
	  def setDenom(x$1 : scala.Int) : scala.Unit = { /* compiled code */ }
	  def this(numer : scala.Int, denom : scala.Int) = { /* compiled code */ }
	}
	```
	
* `javap -p Rational`
	
	```
	public class Rational {
 		private int numer;
 		private int denom;
 		public int numer();
	    public void numer_$eq(int);
	    public void setNumer(int);
	    public int denom();
	    public void denom_$eq(int);
	    public void setDenom(int);
	    public int getNumer();
	    public int getDenom();
	    public Rational(int, int);
	}
	```
		
__加了 `@BeanProperty` 後，就產生了常用的 getter 及 setter 了。因此使用 Scala 來產生 JavaBean, 可以讓工程師少寫幾行程式。__


# Argument

## Default Arguement Values

目前 Java 的參數，並沒有預設值功能。也就是說，沒有辦法寫類似 `public void func(int a = 0) { }` 的程式，而 Scala 卻有這樣功能，因此在程式設計上，就有很大的發揮空間，而且也可以少寫很多程式碼。ex: 

```
class Argu {

  def func(a: Int = 10): Int = a + 1
}
```

* `scalap -private Argu`

	```
	class Argu extends scala.AnyRef {
	  def this() = { /* compiled code */ }
	  def func(a : scala.Int) : scala.Int = { /* compiled code */ }
	}
	```

* `javap -p Argu`

	```
	public class Argu {
  		public int func(int);
		public int func$default$1();
  		public Argu();
	}
	```
	
Scala 在有預設值參數的地方，自行新增一個 function `public int func$default$1();`，當 `func` 被呼叫，且不帶入參數時，Scala 的 compile 就會在 bytecode 偷呼叫 `func$default$1()` 來達到有預設值的效果。

雖然 Scala 允許預設值的參數不一定要放在最後。ex:

```
def func(a: Int, b:Int = 10, c:Int) { }
```
但如果不使用 keyword argument 的方式使用函式，就一定都要傳入參數，ex: `func()(1, 10, 3)` 不能是 `func(1,3)`。因此在設計程式時，建議還是將 default value 的 argument 放在最後。

## Keyword Arguements
keyword arguments 使用跟 python 相同，對於 Java 使用者來說，是一個非常陌生的功能。簡單來說，就是在使用函式時，使用 key-value 方式來傳入參數。 ex:

```
def func(a: Int, b: Int=10, c:Int) { }

func(a = 10, c = 20)
```
這項功能，會讓程式有更高的可讀性，也當然自己在寫程式時，參數的命名也不能隨便。如果函式有比較多的參數要傳入時，也可以透過用 key-value 的方式，讓 coding 的速度可以加快。


# Lazy Load

Lazy 這項功能，Java 的使用者也是非常陌生的功能，簡單來說，你可以先定義變數的來源，等到真的要使用時，再執行產生變數值。ex:

```
scala> def func(a: Int): Int = { 
     | println("call func")
     | a + 1
     | }
func: (a: Int)Int

scala> val x = func(10)
call func
x: Int = 11

scala> lazy val y = func(20)
y: Int = <lazy>

scala> val z = y + 10
call func
z: Int = 31

```

要使用 lazy 的功能，只要在變數前加 `lazy` 這個 keyword 即可，上述的 sample code 中的 `y` 就是一個 lazy 變數，在宣告時，並不會真的執行 `func(20)`，要一直到 `val z = y + 10` 才會真正執行 `func(20)`。

其實 `lazy` 善用的話，也可以幫工程師少寫一些 code。
ex: 以下是連線資料庫的 sample，用來比較 Java 及 Scala 版的差別。

Java 版

```
java.sql.Connection conn = null;
java.sql.PreparedStatement stmt = null;
java.sql.ResultSet rs = null;

try {
  	conn = getConnection()
	stmt = conn.prepareStatement("...........")
  	stmt.setInt(1, 10)
  	rs = stmt.executeQuery()
  
  	while(rs.next()) {
  
  	}
}
catch (Exception ex) {
	ex.printStackTrace();
}
finally {
	close(rs);
	close(stmt);
	close(conn);
	
}
```

Scala 版

```
lazy val conn = getConnection()
lazy val stmt = conn.prepareStatement(".......")
lazy val rs = stmt.executeQuery()

try {
  stmt.setInt(1, 10)
  rs = stmt.executeQuery()
  
  while(rs.next()) {
  
  }
}
catch {
  case ex: Exception => ex.printStackTrace()
}
finally {
  close(rs)
  close(stmt)
  close(conn)
}

```



