# Using REST services

The kvision-remote module contains a `CallAgent` class, which can be used to connect to any RESTfull services \(it will work with any JSON over HTTP services\). You can use remote services with both dynamic and type-safe calls \(using `@Serializable` classes\). The `remoteCall` and `call` methods of `CallAgent` class return `kotlin.js.Promise` object, which can be used directly or easily transformed to the Kotlin coroutine with `asDeferred` extension function.

#### Dynamic parameters, dynamic result

```kotlin
GlobalScope.launch {
    val result: dynamic = callAgent.remoteCall("https://api.github.com/search/repositories", obj { q = "kvision" }).asDeferred().await()
}
```

#### Dynamic parameters, type-safe result

```kotlin
@Serializable
data class Repository(val id: Int, val full_name: String?, val description: String?, val fork: Boolean)

GlobalScope.launch {
    val items: List<Repository> = callAgent.remoteCall("https://api.github.com/search/repositories", obj { q = "kvision" }, deserializer = Repository.serializer().list) {
        it.items
    }.asDeferred().await()
}
```

#### Type-safe parameters, dynamic result

```kotlin
@Serializable
data class Query(val q: String?)

GlobalScope.launch {
    val result: dynamic = callAgent.call("https://api.github.com/search/repositories", Query("kvision")).asDeferred().await()
}
```

#### Type-safe parameters, type-safe result

```kotlin
@Serializable
data class Query(val q: String?)
@Serializable
data class SearchResult(val total_count: Int, val incomplete_results: Boolean)

GlobalScope.launch {
    val searchResult: SearchResult = callAgent.call<SearchResult, Query>("https://api.github.com/search/repositories", Query("kvision")).asDeferred().await()
}
```

