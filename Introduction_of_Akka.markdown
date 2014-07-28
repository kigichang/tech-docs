# Introduction of Akka

Akka 是多工運算的框架，用 Scala 撰寫，也有支援 Java API。Akka 最主要的目的是要解決 Synchronize 造成的效能問題，以及可能發生的 Dead-Lock 問題。

相關資料：

* [Akka 官方資料] (http://akka.io/docs/)
* [Typesafe 的 Activator](https://typesafe.com/platform/getstarted)


## 特性

### Non-Blocking and Asynchronous

所有 Akka 上的運作都是 Non-Blocking，也就是說任何的動作，都會立即回應。因此已經習慣一個指令，等待一個動作的思維，必需要修正，否則寫出來的程式效能不好，也可能有很多 bug。

當然在實際的環境下，我們還是會有 Blocking call 的需求，Scala 本身就有提供這方面的 API 可用。但 Scala 相關的 eco-system 都建議能不用就不用，以免造成效能上的問題。


### Event-Driven, Fire-And-Forgot and Actor

Actor 是 Akka 最基本，也是最重要的元素。 Akka 使用 Actor 來完成工作，並且可以透過自定的 message 來互相溝通，或者觸發指定的工作。最簡單的實作概念：你可以使用 Actor 來管理某個系統資源，但有其他的 Actor 需要使用時，可以透過自定的 message 來操作。舉例來說： `A` 是個管理 Log Queue 的 Actor，負責將傳來的資料，寫入 log 檔案。 當某個 process 有需要寫 log 時，就可以叫出 `A`，並對 `A` 傳送包含要寫入資料的 message。當傳送這個 message 後，process 並不會等待回覆的訊息，而是往下繼續自己的工作。

實作 Actor， 其實也是在實作一個 message 的處理。如果有寫過 Windows 相關的程式，就可以很容易想像。Actor 必需實作一個 `receive` 的 function 來處理當收到某個 message 時，應該要作什麼事情。雷同在 Windows 下某個 Button 被 Click 時，其實是 Windows 系統發出一個 `WM_CLICK` 給 Button，而我們會去實作 `OnClick` funtion 來處理收到 `WM_CLICK`。


### Fault-tolerance and Let It Crash

在多工運算下，容錯是件很重要的事情，Akk 採用 __Let It Crash__ 的思維，會自動重啟已經 crash 的 Actor，當然重啟的 policy 也可以自定，沒有的話，Akka 在發現有 Exception 發生時，會自動重啟 Actor。Actor 本身的狀態或資料，跟 Actor 的工作是被分開的，也就是說，某個 Actor 在執行工作時，不幸 Crash 時，在還沒有被 `Restart`，已經修改的資料，是被保留下來的，此時，你可以選擇 `Restart` 將資料重新 reset 或者用 `Resume` 來繼續往下的工作。


### Location Transparency

Akka 的 Actor 除了可以在本機上執行，也可以指定在遠端的某個機器執行，中間的網路溝通，akka 會自動幫忙處理，但也很重要的一點，你傳送的 message 必須是可以被 serialize。 Akka 是使用 [netty](http://netty.io/) 來處理 network。


## 實作
在實作前，建議去下載 Typesafe 的 Activator 工具，它會自動產生基本 Scala 開發環境設定，省去寫 SBT 的工作。安裝 Activator 很簡單，只要[下載](https://typesafe.com/platform/getstarted)，解開就行了。

用 Activator 產生一個空白的專案：

* 執行 `activator new project_name minimal-scala`
* 在 `build.sbt` 加入 akka library. `libraryDependencies += "com.typesafe.akka" %% "akka-actor" % "2.3.4"`
* 在 `project/plugins.sbt` 加入 eclipse plugin in. `addSbtPlugin("com.typesafe.sbteclipse" % "sbteclipse-plugin" % "2.3.0")`
* 在 project 目錄下，執行 `activator eclipse` 來產生 Eclipse 的專案檔。

### Akka Hello World

按慣例，先來個 hello world。以下的範例會產生一個 Actor ，來接收要對誰說 `hello, world`, 並回覆給原來的傳送者(sender)。

```
package com.example

import akka.actor.Actor
import akka.actor.ActorSystem
import akka.actor.Props
import akka.actor.Inbox
import scala.concurrent.duration._

/* 自定 Message */
case class ToWhom(name: String)
case class Response(msg: String)

/* Actor 回覆 Hi, name. Hello, world! */
class MyActor extends Actor {
  
  def receive = {
    case ToWhom(name) => sender() ! Response(s"Hi, $name. Hello, world!!")
  }
  
}

object HelloWorld extends App {

  /* 啟動 Akka micro-system */
  val system = ActorSystem("HelloWorld")
  
  /* 產生一個 Actor。 */
  val actor = system.actorOf(Props[MyActor], "MyHelloWorld")
  
  /* 看一下 Actor 註冊的 path。如果要重覆使用已產生的 Actor，就需要知道 Actor 的 path。
   * 這個在遠端執行很重要 
   * */
  val path = actor.path
  println(path)
  
  /* demo 使用已產生的 actor */
  val actor2 = system.actorSelection(path)
  
  /* 產生一個收信箱. 等一下用此收件箱的名義，對 MyActor 送訊息 */
  val inbox = Inbox.create(system)
  
  // 以下是多種傳 message 方式，且都是 non-blocking (Fire-and-Forgot)。
  // 方法1
  inbox.send(actor, ToWhom("小明"))
  // 方法2
  //actor.!(ToWhom("小華"))(inbox.getRef)
  // 方法3
  //actor.tell(ToWhom("東東"), inbox.getRef)
  
  // Blocking call，等待回覆訊息，最多等 5 sec。不等的話，程式會往下執行，就結束了。
  val Response(msg) = inbox.receive(5.seconds)
  println(msg)
  
  /* 記得要 shutdown, 要不然程式不會結束 */
  system.shutdown
  
  println("end")
}
```

### 小進階多個 Actor 互傳： 吃飯、睡覺、打東東

實際的情況，都是多個 Actor 彼此間在傳訊息。以下我們就用 __吃飯、睡覺、打東東__ 的笑話來 demo。

```
package com.example.joke

import akka.actor.Actor
import akka.actor.ActorSystem
import akka.actor.Props
import akka.actor.ActorRef
import akka.actor.actorRef2Scala

trait Question
case class Interest() extends Question
case class Why() extends Question

trait Answer
case class Three(name: String, a: String, b: String, c: String) extends Answer
case class Two(name: String, a: String, b: String) extends Answer
case class Because(name: String, msg: String) extends Answer

/**
 * 企鵝
 */
class Penguin(val name: String) extends Actor {
  def receive = {
    case Interest() =>
      sender() ! Three(name, "吃飯", "睡覺", "打東東")
  }
  
  override def preStart() = {
    println(s"$name start")
  }
}

/**
 * 叫東東的企鵝
 */
class DongDong extends Penguin("東東") {
  override def receive = {
    case Interest() =>
      sender() ! Two(name, "吃飯", "睡覺")
    case Why() => sender() ! Because(name, "我就是" + name)
  }
}

/**
 * 記者
 */
class Reporter extends Actor {
  def receive = {
    /* 有三個興趣的回覆 */
    case Three(name, a, b, c) => 
      println(s"$name: $a, $b, $c")
    
    /* 只有二個興趣的回覆，反問 why */      
    case Two(name, a, b) =>
      println(s"$name: $a, $b")
      sender() ! Why()
    
    /* 接到 why 的回覆 */
    case Because(name, msg) =>
      println(s"$name: $msg")
  }
}

object JokeApp extends App {
  val system = ActorSystem("Joke")

  val reporter = system.actorOf(Props[Reporter], "reporter")

  val penguins = new Array[ActorRef](10)

  for (i <- 0 to 8) {
    penguins(i) = system.actorOf(Props(classOf[Penguin], s"Penguin-$i"))
  }

  penguins(9) = system.actorOf(Props[DongDong])

  /* 記者問每隻企鵝，牠的興趣是 */
  penguins.foreach(_.tell(Interest(), reporter))
  
  /* 主程式等一下，要不然上面都是 non-blocking call，會直接結束程式 */
  Thread.sleep(5000)
  system.shutdown
  println("end")
}
```
由上面程式的 output，就可以很明顯感覺到 non-blocking 的威力，即使我們程式是循序產生企鵝的物件，但是它們被初始化的順序卻不一定，傳送訊息也是如此。
