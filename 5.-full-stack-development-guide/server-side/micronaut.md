# Micronaut

[Micronaut](https://micronaut.io) is a modern, JVM-based, fullstack framework for building modular, easily testable microservice and serverless applications. Micronaut provides a simple compile-time aspect-oriented programming API, which is very similar to Spring, but does not use reflection.

## Build configuration

The integration with Micronaut is contained in the `kvision-server-micronaut` module. It has to be added as the dependency in the common target. This module depends on the `micronaut-inject`, `micronaut-http`, `micronaut-router` and `micronaut-websocket`. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("reflect"))
    implementation("io.micronaut:micronaut-inject")
    implementation("io.micronaut.validation:micronaut-validation")
    implementation("io.micronaut.kotlin:micronaut-kotlin-runtime")
    implementation("io.micronaut:micronaut-runtime")
    implementation("io.micronaut:micronaut-http-server-netty")
    implementation("io.micronaut:micronaut-http-client")
    implementation("io.micronaut.session:micronaut-session")
    implementation("io.micronaut:micronaut-jackson-databind")
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
    implementation("jakarta.validation:jakarta.validation-api")
    implementation("ch.qos.logback:logback-classic")
    implementation("org.yaml:snakeyaml")
}
```
{% endcode %}

Micronaut uses Kapt to generate code from various annotations in your application, so you need to configure Kapt:

{% code title="build.gradle.kts" %}
```kotlin
kapt {
    arguments {
        arg("micronaut.processing.incremental", true)
        arg("micronaut.processing.annotations", "com.example.*")
        arg("micronaut.processing.group", "com.example")
        arg("micronaut.processing.module", "template-fullstack-micronaut")
    }
}

dependencies {
    "kapt"(platform("io.micronaut.platform:micronaut-platform:$micronautVersion"))
    "kapt"("io.micronaut:micronaut-inject-java")
    "kapt"("io.micronaut.validation:micronaut-validation")
    "kaptTest"(platform("io.micronaut.platform:micronaut-platform:$micronautVersion"))
    "kaptTest"("io.micronaut:micronaut-inject-java")
}
```
{% endcode %}

## Application configuration

The standard way to configure Micronaut application is `src/jvmMain/resources/application.yml` file. It contains options needed for optional dependencies. It can be empty if they are not used.

## Implementation

### Service class

The implementation of the service class comes down to implementing required interface methods and making it a Micronaut `@Prototype` component.&#x20;

```kotlin
@Prototype
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    override suspend fun getAddressList(search: String?, sort: Sort) {
        return listOf()
    }
    override suspend fun addAddress(address: Address) {
        return Address()
    }
    override suspend fun updateAddress(id: Int, address: Address) {
        return Address()
    }
    override suspend fun deleteAddress(id: Int) {
        return false
    }
}
```

Micronaut IoC (Inversion of Control) allows you to inject resources and other Micronaut components into your service class. You can use standard `@Inject` annotation or constructor based injection.

```kotlin
@Prototype
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    @Inject
    lateinit var context: ApplicationContext

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(context.environment.activeNames)
        return listOf()
    }
    // ...
}
```

You can also inject custom Micronaut components, defined throughout your application.

KVision allows you to inject `HttpRequest` Micronaut object (which can also give you access to the user session, if it is configured)

```kotlin
@Prototype
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {

    @Inject
    lateinit var httpRequest: HttpRequest<*>

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(httpRequest.uri)
        SessionForRequest.find(httpRequest).ifPresent { session ->
            println(session.id)    
        }
        return listOf()
    }
    // ...
}
```

{% hint style="info" %}
Note: The new instance of the service class will be created by Micronaut for every server request. Use session or request objects to store your state with appropriate scope.
{% endhint %}

### **Blocking code**

Since Micronaut architecture is asynchronous and non-blocking, you should not block threads in your application code. If you have to use some blocking code (e.g. blocking I/O, JDBC) use the dedicated coroutine dispatcher.

```kotlin
@Prototype
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    override suspend fun getAddressList(search: String?, sort: Sort) {
        return withContext(Dispatchers.IO) {
            retrieveAddressesFromDatabase(search, sort)
        }
    }
}
```

### Application class

To allow KVision work with Micronaut you have to pass all instances of the `KVServiceManager` objects (defined in common code) to the Micronaut environment. You do this by defining a provider method for the `KVManagers` instance in the main application class.

```kotlin
@Factory
class KVApplication {
    @Bean
    fun getManagers() = KVManagers(listOf(getServiceManager<IAddressService>()))
}

fun main(args: Array<String>) {
    run(*args)
}
```

### Authentication

To secure your application you can use different Micronaut components and ready to use modules. See [Micronaut Security](https://micronaut-projects.github.io/micronaut-security/latest/guide/) guide for details. You can apply different security settings for different services by defining custom `SecurityRule` using KVision `matches` helper function.

```kotlin
import io.kvision.remote.matches

@Singleton
open class AppSecurityRule(rolesFinder: RolesFinder) : AbstractSecurityRule<HttpRequest<*>>(rolesFinder) {
    override fun check(request: HttpRequest<*>, authentication: Authentication?): Publisher<SecurityRuleResult> {
        return if (request.matches(getServiceManager<IAddressService>(), getServiceManager<IProfileService>())) {
            compareRoles(listOf(SecurityRule.IS_AUTHENTICATED), getRoles(authentication))
        } else {
            Mono.just(SecurityRuleResult.ALLOWED)
        }
    }
}
```
