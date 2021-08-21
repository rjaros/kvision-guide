# Legacy Rest Client

The `kvision-jquery` module contains the `io.kvision.jquery.rest.LegacyRestClient` component, which is the exact copy of the jQuery based HTTP client used in KVision 4.x. Use this class if you really need to migrate your application without changing much of your code.

The `LegacyRestClient` class  can be used to connect to any RESTfull services \(it will work with any JSON over HTTP services\). You can use remote services with both dynamic and type-safe calls \(using `@Serializable` classes\). The `remoteCall` and `call` methods of the`LegacyRestClient` class return `kotlin.js.Promise` object.

#### Dynamic parameters, dynamic result

```kotlin
val restClient = LegacyRestClient()
val result: Promise<dynamic> = restClient.remoteCall("https://api.github.com/search/repositories", obj { q = "kvision" })
```

#### Dynamic parameters, type-safe result

```kotlin
@Serializable
data class Repository(val id: Int, val full_name: String?, val description: String?, val fork: Boolean)

val restClient = LegacyRestClient()
val items: Promise<List<Repository>> = restClient.remoteCall("https://api.github.com/search/repositories", obj { q = "kvision" }, deserializer = ListSerializer(Repository.serializer())) {
    it.items
}
```

#### Type-safe parameters, dynamic result

```kotlin
@Serializable
data class Query(val q: String?)

val restClient = LegacyRestClient()
val result: Promise<dynamic> = restClient.call("https://api.github.com/search/repositories", Query("kvision"))
```

#### Type-safe parameters, type-safe result

```kotlin
@Serializable
data class Query(val q: String?)
@Serializable
data class SearchResult(val total_count: Int, val incomplete_results: Boolean)

val restClient = LegacyRestClient()
val searchResult: Promise<SearchResult> = restClient.call<SearchResult, Query>("https://api.github.com/search/repositories", Query("kvision"))
```

{% hint style="info" %}
Note: The `Promise` object can be used directly or easily consumed as the Kotlin coroutine with `await()` extension function \(you need the kotlinx.coroutines library dependency\).
{% endhint %}

There are also `remoteRequest` and `request` methods, which work exactly like above, but return a wrapper `Response` object defined as:

```kotlin
data class Response<T>(val data: T, val textStatus: String, val jqXHR: JQueryXHR)
```

 This object gives you access to the returned data as well as the jQuery XHR object, which allows you to get HTTP header values of the server response.

```kotlin
val restClient = LegacyRestClient()
val result: Promise<Response<dynamic>> = restClient.remoteRequest("https://api.github.com/search/repositories", obj { q = "kvision" })
result.then {
    console.log(it.jqXHR.getResponseHeader("Content-Type"))
}
```

### Custom serializers

`LegacyRestClient` allows you to specify a custom serializers module with a constructor parameter and allows you to use type-safe calls with all types of data.

```kotlin
val module = SerializersModule {
    polymorphic(A::class) {
        subclass(B::class)
        subclass(C::class)
    }
}
val restClient = LegacyRestClient(module)
```

