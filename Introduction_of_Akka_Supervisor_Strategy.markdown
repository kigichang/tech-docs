# Introduction of Akka Supervisor Strategy

每一個 Akka Actor 都會有一個 supervisor。Supervisor 會監控它的子 Actor，並且決定在有 Exception 發生時，該如何處理。我們實作 Actor 時，可以不去 override 它，Akka 本身有預設的 supervisor strategy. 

Akka 的 supervisor strategy 有兩種：

* OneForOneStrategy
* AllForOneStrategy

簡單來說，`OneForOneStrategy` 是其中某個 actor 發生問題時，不會去影響到它的兄弟姊妹，而 `AllForOneStrategy` 則會。Akka 自身預設是 `OneForOneStrategy`。

Akka 提供四種決定，

* Resume：繼續，__不會 reset__ Actor 的資料
* Restart：重啟 Actor，__會 reset__ Actor 的資料
* Stop: 中止 Actor
* Escalate: 將問題往上報，讓上層的 supervisor 來決定。

Akka Actor 有一個特性，就是 Actor 的資料跟工作是分開的，因此我們可以選擇用 `Resume` 的方式，保留資料，繼續往下處理。

Akka 預設的 strategy 處理方式

* `ActorInitializationException` => `Stop`
* `ActorKilledException` => `Stop`
* 其他的 Exception => `Restart`
* 其他的 Throwable => `Escalate`

所以如果我們都不作任何修改的話，當有 `Exception` 發生時，Akka 會自動重啟 Actor。這樣子的方式，已經符合大部分的需求。

參考資料：

* [Akka Supervision and Monitoring](http://doc.akka.io/docs/akka/2.3.4/general/supervision.html)
* [Fault Tolerance](http://doc.akka.io/docs/akka/2.3.4/scala/fault-tolerance.html)


## 實作
[Sample Code](https://github.com/kigichang/akka-strategy-joke)

延用 __吃飯睡覺打東東__ 的企鵝笑話，來 demo Akka Strategy 的功能，名叫 __東東__ 的企鵝，在被打第 __4__ 次時，會說 __不要打擾我__，在第 __6__ 次時，會 __爆炸__，在超過第 __6__ 次後，牠就__成仙__了。本程式，主要是由 `Reporter` 來產生 `Penguin`，也因此 `Reporter` 是 `Penguin` 的 supervisor。各位可以在 `Reporter` 的程式碼中，修改

```
override val supervisorStrategy = 
    OneForOneStrategy(maxNrOfRetries = 10, withinTimeRange = 1 minute) {
    //AllForOneStrategy(maxNrOfRetries = 10, withinTimeRange = 1 minute) {
    
    case _ : DontBotherMeException => Resume
    case _ : ExplodeException => Restart
    case _ : IamGodException => Stop
    case _ : Exception => Escalate
    }
```

或註解掉這一段程式，來了解 Akka Stragegy 的運作方式。

尤其注意在 `Resume` 後，`Penguin` 的 `hit` 值，並沒有被 reset。

你可以依照以下的方式修改程式，觀察輸出的結果，來了解 Akka 的 Supervisor Strategy 運作方式

* 在本 sample code 中，如果都不修改的話，是不會進到 `IamGodException`，因為在 `ExplodeException`後，會重啟 Actor，而 `hit` 值就會被改成 `0`，重頭計算。

* 修改 `ExplodeException => Resume`，則會進到 `IamGodException`, 此時 __東東__ 就會停止了，不能再使用。

* 修改 `ExplodeException => Escalate`，則會發現全部的 `Penguin` actor 都被重啟了，也包括 `Reporter`。

* 將 `OneForOneStrategy` 改成 `AllForOneStrategy`，則如果 __東東__ 被重啟了，其他的 __企鵝__ 也會被重啟。

