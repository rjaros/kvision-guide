# Websockets

In addition to using RPC-style interfaces, KVision support two-way type-safe connections via websockets, based on [Kotlin coroutines channels](https://kotlinlang.org/docs/reference/coroutines/channels.html).

### Common code

The way the websocket connections are defined is in many ways similar to defining remote methods (see previous chapters). You start by declaring two interface methods in the common code, which will be used in the frontend and backend parts. The methods needs to have specific signatures:

```kotlin
suspend (ReceiveChannel<M>, SendChannel<N>) -> Unit
suspend (suspend (SendChannel<M>, ReceiveChannel<N>) -> Unit) -> Unit
```

When you establish the websocket connection, you will be able to send objects of type `M` from the client to the server and send objects of type `N` from the server to the client. Both types `M` and `N` need to fulfill the criteria described in this [chapter](common-code.md#method-parameters-and-return-value-must-be-of-supported-types). Of course type `M` can be the same as `N`.

```kotlin
interface IWsService {
    suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {}
    suspend fun wservice(handler: suspend (SendChannel<Int>, ReceiveChannel<String>) -> Unit) {}
}
```

Both methods need to have the same name and both should have default, empty implementations in the interface.

### Frontend application

To actually create a websocket connection, you call the second method. When the method is called, the connection is established and it lasts as long as the method is running. The connection is terminated when the method is finished. It's a suspending method, so you can easily use loops (even `while(true)` ) and structured concurrency with `coroutineScope` builders.

```kotlin
val ws = getService<IWsService>()
GlobalScope.launch {
  ws.wsservice { output /*: SendChannel<Int>*/, input /*: ReceiveChannel<String>*/ ->
    coroutineScope {
        launch {
            while(true) {
                val i = Random.nextInt()
                output.send(i)
                delay(1000)
            }
        }
        launch {
            for (str in input) {
                println(str)
            }
        }
    }
  }
}
```

### Backend code

{% hint style="info" %}
You need to manually install WebSockets feature in your main method when using Ktor backend.
{% endhint %}

On the backend side you just have to implement the first interface method. It will be automatically called when a new client is connected, and it should run as long as the connection is active.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class WsService : IWsService {
    override suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {
        for (i in input) {
            output.send("I'v got: $i")
        }
    }
}
```

Of course the server can send data to the output channel at any time. So you can even ignore the input at all.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class WsService : IWsService {
    override suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {
        while(true) {
            val i = Random.nextInt()
            output.send("Have a random: $i")
            delay(500)
        }
    }
}
```

If you want to have more control over the client connection and more information about it, you can inject `WebSocketServerSession` when using Ktor module. This object gives you access to, among others, the `ApplicationCall` object.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class WsService : IWsService {
    @Inject
    lateinit var wsSession: WebSocketServerSession

    override suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {
        // wsSession.call.request
    }
}
```

When using Spring Boot module, you can autowire `WebSocketSession` object.

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class WsService : IWsService {
    @Autowired
    lateinit var webSocketSession: WebSocketSession

    override suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {
        //
    }
}
```

When using Javalin, you can inject `WsContext` and `WsSession` objects.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class WsService : IWsService {
    @Inject
    lateinit var wsCtx: WsContext
    @Inject
    lateinit var wsSession: Session

    override suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {
        //
    }
}
```

With Micronaut you can inject `WebSocketSession` object.

```kotlin
@Prototype
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class WsService : IWsService {
    @Inject
    lateinit var webSocketSession: WebSocketSession

    override suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {
        //
    }
}
```

### Disconnection

When the user leaves or closes the browser page the websocket connection is closed. On the backend side you will find both input and output channels closed and then you should return from the backend method.

On the other hand, when the server is stopped the connection is closed as well. But on the frontend side you have the possibility to re-establish the connection when the server is online again. Just run your frontend method again after a few seconds.

```kotlin
val ws = getService<IWsService>()
GlobalScope.launch {
    while (true) {
        ws.wsservice { output, input ->
            coroutineScope {
                // ...
            }
        }
        delay(5000)
    }
}
```
