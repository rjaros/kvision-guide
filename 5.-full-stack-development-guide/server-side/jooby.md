# Jooby

[Jooby](https://jooby.io) is a scalable, fast and modular micro web framework for Java and Kotlin. It is very easy to use and has a lot of different kind, ready to use [modules](https://jooby.io/#modules).&#x20;

## Build configuration

The integration with Jooby is contained in the `kvision-server-jooby` module. It has to be added as the dependency in the common target. This module depends on the `jooby-guice` and `jooby-jackson`. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation(kotlin("reflect"))
    implementation("io.jooby:jooby-netty:$joobyVersion")
    implementation("io.jooby:jooby-hikari:$joobyVersion")
    implementation("io.jooby:jooby-pac4j:$joobyVersion")
    implementation("org.pac4j:pac4j-sql:$pac4jVersion")
    implementation("org.springframework.security:spring-security-crypto:$springSecurityCryptoVersion")
    implementation("commons-logging:commons-logging:$commonsLoggingVersion")
    implementation("com.h2database:h2:$h2Version")
    implementation("org.postgresql:postgresql:$pgsqlVersion")
    implementation("com.github.andrewoma.kwery:core:$kweryVersion")
    implementation("com.github.andrewoma.kwery:mapper:$kweryVersion")
}
```
{% endcode %}

{% hint style="info" %}
Note: You can use other engines instead of Netty - see [Jooby documentation](https://jooby.io/#server). Remember to add the appropriate dependency.
{% endhint %}

## Application configuration

The standard way to configure Jooby application is `src/jvmMain/resources/application.conf` file. It contains options needed for optional modules. It can be empty if no modules are used.

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

The integration module utilizes [Guice](https://github.com/google/guice) and you can access external components and resources by injecting server objects into your class. KVision allows you to inject `Kooby`, `Environment` and `Config` instances, which give you access to the application configuration and also `Context` object, which allow you to access the current request and session information.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    
    @Inject
    lateinit var kooby: Kooby
    @Inject
    lateinit var env: Environment
    @Inject
    lateinit var config: Config

    @Inject
    lateinit var ctx: Context

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(config.getString("option1") ?: "default")
        println(ctx.remoteAddress)
        println(ctx.session().id)
        return listOf()
    }
    // ...
}
```

You can also inject other Guice components, defined in your application and configured in custom Guice modules or with [just-in-time bindings](https://github.com/google/guice/wiki/JustInTimeBindings).

{% hint style="info" %}
Note: The new instance of the service class will be created by Guice for every server request. Use session or request objects to store your state with appropriate scope.
{% endhint %}

### **Blocking code**

Since Jooby execution model assumes suspending endpoints are non-blocking, you should **never** block a thread in your application code. If you have to use some blocking code (e.g. blocking I/O, JDBC) always use the dedicated coroutine dispatcher.

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

This function is the application starting point. It's used to initialize and configure the application. Minimal implementation for KVision integration contains `kvisionInit` and `applyRoutes` function calls.

```kotlin
import io.jooby.runApp
import io.kvision.remote.applyRoutes
import io.kvision.remote.getServiceManager
import io.kvision.remote.kvisionInit

fun main(args: Array<String>) {
    runApp(args) {
        kvisionInit()
        applyRoutes(getServiceManager<IAddressService>())
    }
}
```

Apart from the above KVision configuration you are free to use any other module of the Jooby framework.&#x20;

### Authentication with Pac4j

You can use [Pac4j](https://jooby.io/modules/pac4j/) security module to configure authentication and authorization in your app. By calling `applyRoutes` function before or after Pac4j module declaration, you apply different security requirements to different services.

```kotlin
fun main(args: Array<String>) {
    runApp(args) {
        kvisionInit()
        install(HikariModule("db"))
        applyRoutes(getServiceManager<IRegisterProfileService>()) // No authentication needed
        val config = org.pac4j.core.config.Config()
        config.addAuthorizer("Authorizer") { _, _, _ -> true }
        install(Pac4jModule(Pac4jOptions().apply {
            defaultUrl = "/"
        }, config).client("*", "Authorizer") { _ ->
            FormClient("/") { credentials, context, sessionStore ->
                require(MyDbProfileService::class).validate(credentials as UsernamePasswordCredentials, context, sessionStore)
            }
        })
        applyRoutes(getServiceManager<IAddressService>())    // Authentication needed
        applyRoutes(getServiceManager<IProfileService>())    // Authentication needed
        onStarted {
            val db = require(DataSource::class, "db")
            val session = ThreadLocalSession(db, getDbDialect(require(Config::class)), LoggingInterceptor())
            try {
                AddressDao(session).findById(1)
            } catch (e: Exception) {
                val schema = this.javaClass.getResource("/schema.sql").readText()
                session.update(schema)
            }
        }
    }
}
```
