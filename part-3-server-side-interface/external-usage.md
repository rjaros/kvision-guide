# External usage

The `kvision-remote` module does not depend on the rest of KVision framework. It means that you can use KVision server side interfaces in applications, those client GUI is not based on KVision at all. While it's hard to think of a good reason not to use KVision ;-\), there may be some use cases for that.

### Requirements

Of course the application has to be written in Kotlin and the project must be created with Gradle multiplatform feature. One of the supported frameworks \(Ktor, Jooby,  Spring Boot, Javalin, Vert.x, Micronaut\) must be used on the server side. A client module loader \(e.g. RequireJS\) should be used to load JavaScript modules and a jQuery library must be added as a dependency. Just like in standard configuration, all remote methods must be called with a coroutine context.

### Example

You can find a [simple-mpp-fullstack-ktor](https://github.com/rjaros/kvision-examples/tree/master/simple-mpp-fullstack-ktor) example on GitHub. It's based on the official Ktor [fullstack-mpp](https://github.com/ktorio/ktor-samples/tree/master/mpp/fullstack-mpp) example with only a few changes. You can take a look if you are interested in details. The frontend code looks like this:

```kotlin
@JsName("app")
fun app() {
    val pingService = PingService()

    GlobalScope.launch {
        document.getElementById("js-response")?.textContent = pingService.ping("Hello World from Client!")
    }
}
```

{% hint style="info" %}
The external usage example doesn't use KVision compiler plugin, although it should be possible to create similar project with this plugin.
{% endhint %}

