# Overview

This is a short overview of how to use server-side interface in KVision with the actual example. It contains just the basic concepts and ideas. You can find more information in the following chapters.

Let's assume we want to create an encoder application, that gets some text from the user and encodes it on the server with a chosen algorithm.

### Common source set

We start by defining our data model and service interface in the common \(shared\) source set. We have to declare our "business" method as suspending.

{% code title="Common.kt" %}
```kotlin
enum class EncodingType {
    BASE64, URLENCODE, HEX
}

interface IEncodingService {
    suspend fun encode(input: String, encodingType: EncodingType): String
}
```
{% endcode %}

Next we declare an expected class, which will be implemented in the frontend and backend modules, and define a `KVServiceManager` object, that will allow KVision to do its "magic".

{% code title="Common.kt" %}
```kotlin
expect class EncodingService : IEncodingService

object EncodingServiceManager : KVServiceManager<EncodingService>(EncodingService::class) {
    init {
        GlobalScope.launch(start = CoroutineStart.UNDISPATCHED) {
            bind(IEncodingService::encode)
        }
    }
}
```
{% endcode %}

{% hint style="info" %}
Note: The `GlobalScope.launch` builder is necessary because of the bug in the Kotlin/JS compiler \([KT-27855](https://youtrack.jetbrains.com/issue/KT-27855)\).
{% endhint %}

{% hint style="info" %}
Note: At this point the project will not compile correctly, because `EncodingService` class has no actual declarations.
{% endhint %}

### Frontend source set

To implement `EncodingService` class in our KVision application, we inherit from the `KVRemoteAgent` class and implement the interface with a special `call` method.

{% code title="Frontend.kt" %}
```kotlin
actual class EncodingService : IEncodingService, KVRemoteAgent<EncodingService>(EncodingServiceManager) {
    override suspend fun encode(input: String, encodingType: EncodingType) = 
        call(IEncodingService::encode, input, encodingType)
}
```
{% endcode %}

That's all what is needed to use this service class in the frontend application.

{% code title="FrontendApp.kt" %}
```kotlin
val service = EncodingService()

vPanel {
    val input = textAreaInput()
    val select = selectInput(listOf(EncodingType.BASE64.name to "Base64",
        EncodingType.URLENCODE.name to "URL Encode", EncodingType.HEX.name to "Hex"))
    val button = button("Encode")
    val output = div()
    button.onClick {
        GlobalScope.launch {
            val encodingType = select.value?.let { EncodingType.valueOf(it) } ?: EncodingType.BASE64
            val result = service.encode(input.value ?: "", encodingType)
            output.content = result
        }
    }
}
```
{% endcode %}

Notice we just call the `encode` method and get the result value directly. All asynchronous operations are hidden by the framework. We only have to use a coroutine builder function \(`launch` in this case\).

### Backend source set

_\(For convenience we will use Ktor module in this chapter\)_

To create an actual implementation of our `EncodingService` we just have to implement the methods of the service interface.

{% code title="Backend.kt" %}
```kotlin
import java.net.URLEncoder
import acme.Base64Encoder
import acme.HexEncoder

actual class EncodingService : IEncodingService {
    override suspend fun encode(input: String, encodingType: EncodingType): String {
        return when (encodingType) {
                EncodingType.BASE64 -> {
                    Base64Encoder.encode(input)
                }
                EncodingType.URLENCODE -> {
                    URLEncoder.encode(input, "UTF-8")
                }
                EncodingType.HEX -> {
                    HexEncoder.encode(input)
                }
        }
    }
}
```
{% endcode %}

Finally, we initialize routing in the main application function.

{% code title="Main.kt" %}
```kotlin
import io.ktor.application.Application

fun Application.main() {
    kvisionInit()
    routing {
        applyRoutes(EncodingServiceManager)
    }
}
```
{% endcode %}

When we run our application everything will work automatically - a call on the client side will run the code on the server and the result will be sent back to the caller.

That's all - our first, full-stack KVision application is ready!

You can find "encoder-fullstack-ktor", a complete application based on this overview \(with some visual enhancements of the GUI\), in the [kvision-examples](https://github.com/rjaros/kvision-examples) repository on GitHub.

