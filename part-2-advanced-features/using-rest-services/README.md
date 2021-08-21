# Using REST services

The `io.kvision.rest.RestClient` component, located in the `kvision-rest` module, can be used to connect to any RESTfull services \(it will work with any JSON over HTTP services\). You can use remote services with both dynamic and type-safe calls \(using `@Serializable` classes\). The `RestClient` class has only a single `receive()` method, which uses the builder pattern for configuration and returns a `kotlin.js.Promise<RestResponse<T>>` object. A number of extension functions is defined for `RestClient`, which allow you to make typical calls easier.

#### Dynamic parameters, dynamic result

```kotlin
val restClient = RestClient()
val result: Promise<dynamic> = restClient.callDynamic("https://api.github.com/search/repositories") {
    data = obj { q = "kvision" }
}
```

#### Dynamic parameters, type-safe result

```kotlin
@Serializable
data class Repository(val id: Int, val full_name: String?, val description: String?, val fork: Boolean)

val restClient = RestClient()
val items: Promise<List<Repository>> = restClient.call("https://api.github.com/search/repositories") {
    data = obj { q = "kvision" }
    resultTransform = { it.items }
}
```

#### Type-safe parameters, dynamic result

```kotlin
@Serializable
data class Query(val q: String?)

val restClient = RestClient()
val result: Promise<dynamic> = restClient.callDynamic("https://api.github.com/search/repositories", Query("kvision"))
```

#### Type-safe parameters, type-safe result

```kotlin
@Serializable
data class Query(val q: String?)
@Serializable
data class SearchResult(val total_count: Int, val incomplete_results: Boolean)

val restClient = RestClient()
val searchResult: Promise<SearchResult> = restClient.call("https://api.github.com/search/repositories", Query("kvision"))
```

{% hint style="info" %}
Note: The `Promise` object can be used directly or easily consumed as the Kotlin coroutine with `await()` extension function \(you need the kotlinx.coroutines library dependency\).
{% endhint %}

A wrapper `RestResponse` class is defined as:

```kotlin
data class RestResponse<T>(val data: T, val textStatus: String, val response: Response)
```

When using `receive()` ,`request()` or `requestDynamic()` functions, the returned `RestResponse` object gives you access to the returned data as well as the native `Response` object, which gives you access to the server response \(e.g. to get HTTP header values and other information\): 

```kotlin
val restClient = RestClient()
val result: Promise<RestResponse<dynamic>> = restClient.requestDynamic("https://api.github.com/search/repositories") {
    data = obj { q = "kvision" }
}
result.then {
    console.log(it.response.headers.get("Content-Type"))
}
```

### Custom serializers

`RestClient` allows you to specify a custom serializers module with a builder parameter and allows you to use type-safe calls with all types of data.

```kotlin
val module = SerializersModule {
    polymorphic(A::class) {
        subclass(B::class)
        subclass(C::class)
    }
}
val restClient = RestClient {
    serializersModule = module
}
```

