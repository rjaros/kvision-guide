# Ktor

[Ktor](https://ktor.io/) is the asynchronous web framework for Kotlin created by JetBrains. It is written in Kotlin and its machinery and API is utilizing Kotlin coroutines. It's unopinionated design makes it a very good choice for development of applications composed of many different technologies. Since both Ktor and KVision are based on coroutines, the plugging of KVision's server module into Ktor's pipeline is executed in a natural and efficient way.

## Build configuration

The integration with Ktor is contained in the `kvision-server-ktor` module. It has to be added as the dependency in the common target. This module depends on the `ktor-server-core`, `ktor-serialization`, and `guice` libraries. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation(kotlin("reflect"))
    implementation("io.ktor:ktor-server-netty:$ktorVersion")
    implementation("io.ktor:ktor-auth:$ktorVersion")
    implementation("ch.qos.logback:logback-classic:$logbackVersion")
    implementation("com.h2database:h2:$h2Version")
    implementation("org.jetbrains.exposed:exposed:$exposedVersion")
    implementation("org.postgresql:postgresql:$pgsqlVersion")
    implementation("com.zaxxer:HikariCP:$hikariVersion")
    implementation("commons-codec:commons-codec:$commonsCodecVersion")
    implementation("com.axiomalaska:jdbc-named-parameters:$jdbcNamedParametersVersion")
    implementation("com.github.andrewoma.kwery:core:$kweryVersion")
}
```
{% endcode %}

{% hint style="info" %}
Note: You can use other engines instead of Netty - see [Ktor documentation](https://ktor.io/docs/engines.html). Remember to add the appropriate dependency.
{% endhint %}

## Application configuration

The standard way to configure Ktor application is `src/jvmMain/resources/application.conf` file. Among other options it contains the name of the main function of your app.

{% code title="application.conf" %}
```
ktor {
  deployment {
    port = 8080
    watch = [server/build/classes]
  }
  application {
    modules = [com.example.MainKt.main]
  }
}
```
{% endcode %}

## Implementation

### Service class

The implementation of the service class comes down to implementing required interface methods.

```kotlin
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

The integration module utilizes [Guice](https://github.com/google/guice) and you can access external components and resources by injecting server objects into your class. KVision allows you to inject `Application` and `ApplicationCall` instances. These objects give you access to the application configuration, its state, the current request and the user session (if it is configured).

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    @Inject
    lateinit var application: Application
    @Inject
    lateinit var call: ApplicationCall

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(application.environment.config.propertyOrNull("option1")?.getString())
        println(application.attributes)
        println(call.request.uri)
        println(call.request.queryParameters["param1"])
        println(call.sessions)
        return listOf()
    }
    // ...
}
```

You can also inject other Guice components, defined in your application and configured in custom Guice modules or with [just-in-time bindings](https://github.com/google/guice/wiki/JustInTimeBindings).

{% hint style="info" %}
Note: The new instance of the service class will be created by Guice for every server request. Use application, session or call objects to store your state with appropriate scope.
{% endhint %}

### **Blocking code**

Since Ktor architecture is asynchronous and non-blocking, you should **never** block a thread in your application code. If you have to use some blocking code (e.g. blocking I/O, JDBC) always use the dedicated coroutine dispatcher.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    override suspend fun getAddressList(search: String?, sort: Sort) {
        return withContext(Dispatchers.IO) {
            retrieveAddressesFromDatabase(search, sort)
        }
    }
}
```

### The main function

This function is the application starting point. It's used to initialize and configure application modules and features. Minimal implementation for KVision integration contains `kvisionInit` and `applyRoutes` function calls. `kvisionInit` function should be executed as last.

```kotlin
import io.ktor.application.Application
import io.ktor.routing.routing
import io.kvision.remote.applyRoutes
import io.kvision.remote.getServiceManager
import io.kvision.remote.kvisionInit

fun Application.main() {
    routing {
        applyRoutes(getServiceManager<IAddressService>())
    }
    kvisionInit()
}
```

The `kvisionInit` function can take multiple parameters of type `com.google.inject.Module`. It allows you to register custom modules inside the main Guice injector.

```kotlin
fun Application.main() {
    routing {
        applyRoutes(getServiceManager<IAddressService>())
    }
    kvisionInit(HelloModule())
}

class HelloModule() : AbstractModule() {
    override fun configure() {}

    @Provides
    @Singleton
    @Named("hello-message")
    fun helloMessage(): String = "Hello from Guice"
}
```

Apart from the above KVision configuration you are free to use any other module or feature of the Ktor framework.

### Authentication

When using [authentication feature](https://ktor.io/servers/features/authentication.html) you can choose where to apply KVision routes for different services.

```kotlin
fun Application.main() {
    install(Compression)
    install(DefaultHeaders)
    install(CallLogging)
    install(Sessions) {
        cookie<Profile>("KTSESSION", storage = SessionStorageMemory()) {
            cookie.path = "/"
            cookie.extensions["SameSite"] = "strict"
        }
    }
    Db.init(environment.config)

    install(Authentication) {
        form {
            userParamName = "username"
            passwordParamName = "password"
            validate { credentials ->
                // ...
            }
            skipWhen { call -> call.sessions.get<Profile>() != null }
        }
    }

    routing {
        applyRoutes(getServiceManager<IRegisterProfileService>()) // No authentication needed
        authenticate {
            post("login") {
                // ...
            }
            get("logout") {
                call.sessions.clear<Profile>()
                call.respondRedirect("/")
            }
            applyRoutes(getServiceManager<IAddressService>()) // Authentication needed
            applyRoutes(getServiceManager<IProfileService>()) // Authentication needed
        }
    }
    kvisionInit()
}
```
