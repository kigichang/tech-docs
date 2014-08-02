# Introduction of Akka Routing

當產生許多子 actor 時， Akka 提供 `Router` 的機制，來有效分配 message 給子 actor 來完成工作。 在 Akka 中，受 `Router` 管理的 actor 稱作 `Routee`。

Akka 提供以下 routing 的管理方式：

* `akka.routing.RoundRobinRoutingLogic`
* `akka.routing.RandomRoutingLogic`
* `akka.routing.SmallestMailboxRoutingLogic`
* `akka.routing.BroadcastRoutingLogic`
* `akka.routing.ScatterGatherFirstCompletedRoutingLogic`
* `akka.routing.ConsistentHashingRoutingLogic`

參考資料：

[Akka Routing](http://doc.akka.io/docs/akka/2.3.4/scala/routing.html)


實作：

[Sample Code](https://github.com/kigichang/akka-route-joke)

延用 __吃飯、睡覺、打東東__ 的範列，這次記者會打所有的企鵝，共 100 次，每隻企鵝會記錄被打了幾次，並回報給記者。本程式，主要是由 `Reporter` 來產生 `Penguin`，並且利用一個 `Router`物件來管理所有的  `Penguin`。

如何使用 `Router`：

```
 val penguins = new Array[ActorRefRoutee](10)

  for (i <- 0 to 8) {
    val actor = context.actorOf(Props(classOf[Penguin], s"Penguin-$i"))
    context watch actor
    penguins(i) = ActorRefRoutee(actor)
    actor ! Identify(actor.path.toString())
  }

  val dong = context.actorOf(Props[DongDong], "dongdong")
  context watch dong
  penguins(9) = ActorRefRoutee(dong)
  dong ! Identify(dong.path.toString())

  import context.dispatcher
  context.setReceiveTimeout(5 seconds) // 設定 timeout 5 seconds
  //var router = Router(SmallestMailboxRoutingLogic(), penguins.toIndexedSeq)
  var router = Router(RoundRobinRoutingLogic(), penguins.toIndexedSeq)
```

使用 Router 很簡單，首先將產生的 `ActorRef` 利用 `ActorRefRoutee`，再加入 `Router` 並設定此 `Router` 要使用那種管理方式，如上例使用 `RoundRobinRoutingLogic`。

如果要進一步監控子 actor，可以使用 `watch` ，如上例 `context watch actor`，當有子 actor 中止時， supervisor 會收到 `Terminated` 的訊息，其中會指名那個子 actor 已經中止。如下：

```
case Terminated(child) =>
      router = router.removeRoutee(child)
      val actor = 
        if (child.path.toString().indexOf("dongdong") >= 0)
          context.actorOf(Props[DongDong], "dongdong")
        else
          context.actorOf(Props(classOf[Penguin], "Penguin" + (System.nanoTime())))

      context watch actor
      router = router.addRoutee(actor)
```

如果要測試不同的 routing 的方式，則修改 `var router = Router(RoundRobinRoutingLogic(), penguins.toIndexedSeq)` 即可，此範例還撘配 Supervisor Strategy 來測試。


