# Scala Pattern and Extractor

一般常用的 Pattern Match 

```
scala> case class Test(a: Int)
defined class Test

scala> val test = Test(10)
test: Test = Test(10)

scala> test match {
     | case Test(n) => println(n)
     | case _ => println("not match")
     | }
10

scala>
```

## Extractor
一個 _Extractor_ 需要有以下其中一個

* unapply
* unapplySeq

這類的 function ，稱為 __extraction__；相反的，`apply` 則稱為 __injection__。

* Extractor 只要有實作 `unapply` or `unapplySeq` 即可；但如果 Extractor 沒有實作 `apply`, 則 `unapply` 回傳型別必須是 `Boolean`。

	`unapplySeq` 是用在 __variable argument__ 也就是類似 `func(lst: String*)`，可以不實作 `apply` 也會有 `apply` 的效果。


* Extractor 可以是 `object` or `class`。`class` 可以存當時的條件，但 `object` 則沒有這樣的效果 (因為 object 是 singleton，無法存每次不同的比對條件)


## Pattern, Extractor, and Binding

* Extractor only with extraction and binding (sample from [Programming in Scala: A Comprehensive Step-by-Step Guide, 2nd Edition](http://www.amazon.com/Programming-Scala-Comprehensive-Step-Step/dp/0981531644))

	```
	object EMail {
	
	  def apply(u: String, d: String) = { u + "@" + d }	<-- Injection
	  
	  def unapply(s: String): Option[(String, String)] = {	<-- Extraction
	    var parts = s.split("@")
	    if (parts.length == 2) Some(parts(0), parts(1)) else None
	  }
	}
	
	object UpperCase {
	  def unapply(s: String): Boolean = s == s.toUpperCase()	<-- Extraction Only
	}
	
	
	object pattern extends App {
	
	  "Test@test.com" match {
	    case EMail(user @ UpperCase(), domain) => println(user, domain)	<-- 注意：UpperCase 後面一定要加 () (括號)
	    case _ => println("not match")
	  }
	  
	  "TEST@test.com" match {
	    case EMail(user @ UpperCase(), domain) => println(user, domain)
	    case _ => println("not match")
	  }
	}
	```

	`Test@test.com` 結果是 `not match`, 因為在 `UpperCase` 是 `false`. `TEST@test.com` 則是 `(TEST, test.com)`
	
	
* Extractor with variable arguement


	```
	class Between(val min: Int, val max: Int) {
	  
	  def unapplySeq(value: Int): Option[List[Int]] = 	<-- Extraction Only
	    if (min <= value && value <= max) Some(List(min, value, max))
	    else None
	}
	
	object pattern extends App {
	
	  val between5and15 = new Between(5, 15)
	  
	  10 match {
	    case between5and15(min, value, max) => println(value)
	    case _ => println("not match")
	  }
	  
	  20 match {
	    case between5and15(min, value, max) => println(value)
	    case _ => println("not match")
	  }
	
	}
	```

	因為 `Between` 的 `unapplySeq` 回傳是 `List(min, value, max)`，所以比對的 pattern 就必須是 List 的 pattern，像 `(min, value, max)` or `(_, value, max)` or `(min, _*)`
	
* Extractor with binding

	```
	class Between(val min: Int, val max: Int) {
	  
	  def unapplySeq(value: Int): Option[List[Int]] = 
	    if (min <= value && value <= max) Some(List(min, value, max))
	    else None
	}
	
	object pattern extends App {
	
	  val between5and15 = new Between(5, 15)
	  
	  (50, 10) match {
	    case (n @ between5and15(_*), _) => println("first match " + n)
	    case (_, m @ between5and15(_*)) => println("second match " + m)
	    case _ => println("not match")
	  }
	}
	```

	`class` 的 Extractor 用在 binding 時，要注意要附上比對的 pattern (ex: `between5and15(_*)`)，如果沒寫對，會比對失敗。比如說：把 `(_, m @ between5and15(_*))` 改成 `case (_, m @ between5and15())`, 雖然 m (m = 10) 在 5 ~ 15，但會比對失敗。
	
	
## Pattern and Regex

Scala 的 Regex 有實作 `unapplySeq`, Regex 搭配 Pattern 非常好用。

```
val digits = """(\d+)-(\d+)""".r
  
"123-456" match {
  case digits(a, b) => println(a, b)
  case _ => println("not match")
}

"123456" match {
  case digits(a, b) => println(a, b)
  case _ => println("not match")
}

"abc-456" match {
  case digits(a, b) => println(a, b)
  case _ => println("not match")
}
  
```

因為 `digits` 有用到 `group`，所以 pattern 會是 `digits(a, b)`。如果把 `val digits = """(\d+)-(\d+)""".r` 改成 `val digits = """\d+-\d+""".r` 不使用 group 時，因為比對的 pattern 改變 (`digits(a, b)` -> `digits()`)，所以上面的三個比對都會是 `not match`。需要將程式改成如下，才會正確

```
val digits = """\d+-\d+""".r
  
"123-456" match {
  case digits() => println("ok")
  case _ => println("not match")
}
  
```

所以使用 `Regex` 時，儘量用 `group` 的功能，在系統設計時，彈性會比較大。


### Regex and Binding

```
val digits = """(\d+)-(\d+)-(\d+)""".r

("123-abc-789", "123-456-789") match {
  case (_ @ digits(a, _*), _) => println(a)
  case (_, _ @ digits(a, b, c)) => println(a, b, c)
  case _ => println("not match")
}
```

用 Binding 時，一樣要注意比對的 pattern，如： `digits(a, _*)`, `digits(a, b, c)`
