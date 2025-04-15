# Common Code

## Build configuration

You have to declare the dependencies on one of the `kvision-server-*` modules in the common target.

{% code title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-common"))
//    api("io.kvision:kvision-server-javalin:$kvisionVersion")
//    api("io.kvision:kvision-server-jooby:$kvisionVersion")
    api("io.kvision:kvision-server-ktor:$kvisionVersion")
//    api("io.kvision:kvision-server-ktor-koin:$kvisionVersion")
//    api("io.kvision:kvision-server-spring-boot:$kvisionVersion")
//    api("io.kvision:kvision-server-vertx:$kvisionVersion")
//    api("io.kvision:kvision-server-micronaut:$kvisionVersion")
}
```
{% endcode %}

## Implementation

The common sources is the place where you define how your remote services should look and how they should work. You can define as many services as you wish, and they can have as many methods as you need. It's a good practice to split your services based on their context and functions.

{% hint style="info" %}
Note: When using authentication on the server side, you can usually apply different authentication options to different services.
{% endhint %}

{% hint style="info" %}
Note: This guide assumes you are using KVision compiler plugin to generate code for server-side interfaces. You can write this code manually if you want - for details please check the guide for [KVision 1.x](https://kvision.gitbook.io/kvision-guide/v/kvision-1.x/part-3-server-side-interface/common-code).&#x20;
{% endhint %}

### Declaring an interface

Designing the interface is probably the most important step, and during this process you have to stick to some important rules.

#### An interface name need to start with `'I'` and end with `'Service'` phrases and must be annotated with `@KVService` annotation.

This convention allows KVision compiler plugin to generate common, backend and frontend code.

#### A method must be suspending

This requirement allows the framework to translate asynchronous calls into synchronous-like code.

#### A method must have from zero to six parameters

This is the restriction of the current version of the framework. It may change in the future.

#### A method can't return nullable value

`Unit` return type is not supported as well.

#### Method parameters and return value must be of supported types

Supported types are:

* all basic Kotlin types (`String`, `Boolean`, `Int`, `Long`, `Short`, `Char`, `Byte`,  `Float`, `Double`)
* `Enum` classes defined in common code annotated with `@Serializable` annotation
* All date and time types from `io.kvision.types` package, which are automatically mapped to `kotlin.js.Date` on the frontend side and the appropriate `java.time.*` type on the backend side
* A `io.kvision.types.Decimal` type, which is automatically mapped to `Double` on the frontend side and `java.math.BigDecimal` on the backend side
* any class defined in the common code with a `@Serializable` annotation
* a `List<T>`, where T is one of the above types
* a `T?`, where T is one of the above types (allowed only as method parameters - see previous rule)
* a `Result<T>`, where T is one of the above types, can be used as a method return value.

{% hint style="info" %}
Note: Default parameters values are supported.
{% endhint %}

Even with the above restrictions, the set of supported types is quite rich and you should be able to model almost any use case for your applications. With the help of `@Serializable` annotation you can always wrap any data structure into a serializable data class. It's also a simple way to pass around the parameters count limit.

{% hint style="info" %}
Note: You have to add `@Contextual` annotations to your `LocalDate`, `LocalDateTime`, `LocalTime`, `OffsetDateTime,` `OffsetTime, ZonedDateTime` and `Decimal` fields in order to explicitly allow serialization with the KVision context. You can also use `@file:UseContextualSerialization(LocalDate::class)` file annotation if you want to keep your model classes cleaner. You can find more information about this annotation in the [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md#contextual-serialization) documentation.
{% endhint %}

With an interface defined in the common code, the type safety of your whole application is forced at a compile time. Any incompatibility between the frontend and the backend code will be marked as a compile-time error.

```kotlin
import io.kvision.annotations.KVService

@Serializable
data class Address(
    val id: Int? = 0,
    val firstName: String? = null,
    val lastName: String? = null,
    val email: String? = null,
)

@Serializable
enum class Sort {
    FN, LN, E
}

@KVService
interface IAddressService {
    suspend fun getAddressList(search: String?, sort: Sort): List<Address>
    suspend fun addAddress(address: Address): Address
    suspend fun updateAddress(id: Int, address: Address): Address
    suspend fun deleteAddress(id: Int): Boolean
}
```

### Using annotations to change HTTP method and/or route name

By default KVision will use HTTP POST server calls and automatically generated route names. But you can use `@KVBinding`, `@KVBindingMethod` and `@KVBindingRoute` annotations on any interface method, to change the default values.

```kotlin
@KVService
interface IAddressService {
    @KVBindingRoute("get_address_list")
    suspend fun getAddressList(search: String?, sort: Sort): List<Address>
    @KVBinding(Method.PUT, "add_address")
    suspend fun addAddress(address: Address): Address
    @KVBindingRoute("update_address")
    suspend fun updateAddress(id: Int, address: Address): Address
    @KVBinding(Method.DELETE, "delete_address")
    suspend fun deleteAddress(id: Int): Boolean
}
```

{% hint style="info" %}
Note: All KVision endpoint names (even those with user defined names) are prefixed with "/kv/" to avoid potential conflicts with other endpoints.
{% endhint %}
