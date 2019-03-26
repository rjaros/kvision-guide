# Using REST services

The `pl.treksoft.kvision.rest.RestClient` class can be used to connect to any RESTfull services \(it will work with any JSON over HTTP services\). You can use remote services with both dynamic and type-safe calls \(using `@Serializable` classes\). The `remoteCall` and `call` methods of `RestClient` class return `kotlin.js.Promise` object.

#### Dynamic parameters, dynamic result

```kotlin
val restClient = RestClient()
val result: Promise<dynamic> = restClient.remoteCall("https://api.github.com/search/repositories", obj { q = "kvision" })
```

#### Dynamic parameters, type-safe result

```kotlin
@Serializable
data class Repository(val id: Int, val full_name: String?, val description: String?, val fork: Boolean)

val restClient = RestClient()
val items: Promise<List<Repository>> = restClient.remoteCall("https://api.github.com/search/repositories", obj { q = "kvision" }, deserializer = Repository.serializer().list) {
    it.items
}
```

#### Type-safe parameters, dynamic result

```kotlin
@Serializable
data class Query(val q: String?)

val restClient = RestClient()
val result: Promise<dynamic> = restClient.call("https://api.github.com/search/repositories", Query("kvision"))
```

#### Type-safe parameters, type-safe result

```kotlin
@Serializable
data class Query(val q: String?)
@Serializable
data class SearchResult(val total_count: Int, val incomplete_results: Boolean)

val restClient = RestClient()
val searchResult: Promise<SearchResult> = restClient.call<SearchResult, Query>("https://api.github.com/search/repositories", Query("kvision"))
```

{% hint style="info" %}
Note: The `Promise` object can be used directly or easily transformed to the Kotlin coroutine with `asDeferred` extension function \(you need the kotlinx.coroutines library dependency\).
{% endhint %}



