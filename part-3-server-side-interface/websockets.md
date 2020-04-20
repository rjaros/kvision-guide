# Websockets

In addition to using RPC-style interfaces, KVision support two-way type-safe connections via websockets, based on [Kotlin coroutines channels](https://kotlinlang.org/docs/reference/coroutines/channels.html). 

{% hint style="info" %}
Note: Since channels API in Kotlin is still marked as experimental, you should treat websockets support in KVision as experimental feature, too.
{% endhint %}

### Common code

The way the websocket connections are defined is in many ways similar to defining remote methods \(see previous chapters\). You start by declaring an interface method in the common code, which will be used in the frontend and backend parts. This method needs to have a specific signature:

```kotlin
suspend (ReceiveChannel<M>, SendChannel<N>) -> Unit
```

When you establish the websocket connection, you will be able to send objects of type `M` from the client to the server and send objects of type `N` from the server to the client. Both types `M` and `N` need to  fulfill the criteria described in this [chapter](common-code.md#method-parameters-and-return-value-must-be-of-supported-types). Of course type `M` can be the same as `N`.

#### Service manager object

{% hint style="info" %}
This is not required if you use KVision compiler plugin.
{% endhint %}

The declared interface method has to be passed to the call of a `bind` method of the `KVServiceManager` object.

```kotlin
interface IWsService {
    suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {}
}

expect class WsService : IWsService

object WsServiceManager : KVServiceManager<WsService>(WsService::class) {
    init {
        GlobalScope.launch(start = CoroutineStart.UNDISPATCHED) {
            bind(IWsService::wservice)
        }
    }
}
```

### Frontend code

{% hint style="info" %}
This is not required if you use KVision compiler plugin.
{% endhint %}

On the frontend side you implement the actual class, using `webSocket` methods from the `KVRemoteAgent` class. Note that, unlike the implementation of remote methods, you do not implement the interface `wservice` method inside the actual class \(it wont be used directly and it can have the empty implementation `{}` already added inside the interface declaration\). Instead, you create a method, which takes a specific `handler` function as a parameter. The handler's signature is:

```kotlin
suspend (SendChannel<M>, ReceiveChannel<N>) -> Unit
```

The types are reversed \(!\), because on the frontend side we will send `M` and receive `N` objects. Our implementation could look like this:

```kotlin
actual class WsService : IWsService, KVRemoteAgent<WsService>(WsServiceManager) {
    suspend fun wservice(handler: suspend (SendChannel<Int>, ReceiveChannel<String>) -> Unit) =
        webSocket(IWsService::wservice, handler)
}
```

### Frontend application

Now you are ready to use this method and actually create a websocket connection. The idea is simple - when the method is called, the connection is established and it lasts as long as the method is running. The connection is terminated when the method is finished. It's a suspending method, so you can easily use loops \(even `while(true)` \)  and structured concurrency with `coroutineScope` builders.

```kotlin
val ws = WsService()
GlobalScone.launch {
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

On the backend side you just have to implement the interface method. It will be automatically called when a new client is connected, and it should run as long as the connection is active.

```kotlin
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
actual class WsService : IWsService {
    @Inject
    lateinit var wsSession: WebSocketServerSession

    override suspend fun wservice(input: ReceiveChannel<Int>, output: SendChannel<String>) {
        // wsSession.call.request
    }
}
```

When using Spring Boot module, you can use `WithWebSocketSession` interface.

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
actual class WsService : IWsService, WithWebSocketSession {
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
val ws = WsService()
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

