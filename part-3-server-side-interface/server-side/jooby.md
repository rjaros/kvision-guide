# Jooby

[Jooby](https://jooby.org/) is a scalable, fast and modular micro web framework for Java and Kotlin. It is very easy to use and has a lot of different kind, ready to use [modules](https://jooby.org/modules/). 

## Build configuration

The integration with Jooby is contained in the `kvision-server-jooby` module. It has to be added as the dependency in the backend target. This module depends on the `jooby-lang-kotlin`, `jooby-jackson`, `jooby-pac4j2` and `jackson-module-kotlin` libraries. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code-tabs %}
{% code-tabs-item title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation(kotlin("reflect"))
    implementation("pl.treksoft:kvision-server-jooby:$kvisionVersion")
    implementation("org.jooby:jooby-netty:$joobyVersion")
    implementation("org.jooby:jooby-jdbc:$joobyVersion")
    implementation("org.jooby:jooby-pac4j2:$joobyVersion")
    implementation("org.pac4j:pac4j-sql:$pac4jVersion")
    implementation("org.springframework.security:spring-security-crypto:$springSecurityCryptoVersion")
    implementation("commons-logging:commons-logging:$commonsLoggingVersion")
    implementation("com.h2database:h2:$h2Version")
    implementation("org.postgresql:postgresql:$pgsqlVersion")
    implementation("com.github.andrewoma.kwery:core:$kweryVersion")
    implementation("com.github.andrewoma.kwery:mapper:$kweryVersion")
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
Note: You can use other engines instead of Netty - see [Jooby documentation](https://jooby.org/doc/servers/). Remember to add the appropriate dependency.
{% endhint %}

## Application configuration

The standard way to configure Jooby application is `src/backendMain/resources/application.conf` file. It contains options needed for optional modules. It can be empty if no modules are used.

## Implementation

### Service class

The implementation of the service class comes down to implementing required interface methods.

```kotlin
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

Jooby is based on [Guice](https://github.com/google/guice) and you can access external components and resources by injecting server objects into your class. Most notably the `Config` and the `Request` objects, which give you access to the application configuration, the current request and the user session \(with the `request.session()` method\).

```kotlin
actual class AddressService : IAddressService {
    @Inject
    lateinit var config: Config
    @Inject
    lateinit var request: Request

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(config.getString("option1") ?: "default")
        println(request.path())
        println(request.get<String>("param1"))
        println(request.session())
        return listOf()
    }
    // ...
}
```

You can also inject other components, defined in different Jooby modules, configured in your custom modules and with [just-in-time bindings](https://github.com/google/guice/wiki/JustInTimeBindings).

{% hint style="info" %}
Note: Because of the threading model of Kotlin coroutines, you should prefer dependency injection over using the [`require`](https://jooby.org/doc/#request-require) method.
{% endhint %}

{% hint style="info" %}
Note: The new instance of the service class will be created by Guice for every server request. Use session or request objects to store your state with appropriate scope.
{% endhint %}

### Application class

This class is the application starting point. It's used to initialize and configure the application. Minimal implementation for KVision integration contains `kvisionInit` and `applyRoutes` function calls.

```kotlin
import org.jooby.Jooby.run
import org.jooby.Kooby
import pl.treksoft.kvision.remote.applyRoutes
import pl.treksoft.kvision.remote.kvisionInit

class App : Kooby({
    kvisionInit()
    applyRoutes(AddressServiceManager)
})

fun main(args: Array<String>) {
    run(::App, args)
}
```

Apart from the above KVision configuration you are free to use any other module of the Jooby framework. 

### Authentication with Pac4j

KVision is already integrated with [Pac4j](https://jooby.org/doc/pac4j2/) security module and you can use the `Profile` class in common, frontend and backend code. By calling `applyRoutes` function before or after Pac4j module declaration, you apply different security requirements to different services.

```kotlin
class App : Kooby({
    kvisionInit()
    use(Jdbc("db"))
    applyRoutes(RegisterProfileServiceManager) // No authentication needed
    use(Pac4j().client { _ ->
        FormClient("/") { credentials, context ->
            require(MyDbProfileService::class).validate(credentials as UsernamePasswordCredentials, context)
        }
    })
    applyRoutes(AddressServiceManager)        // Authentication needed
    applyRoutes(ProfileServiceManager)        // Authentication needed
    onStart {
        val db = require("db", DataSource::class)
        val session = ThreadLocalSession(db, getDbDialect(require(Config::class)), LoggingInterceptor())
        try {
            AddressDao(session).findById(1)
        } catch (e: Exception) {
            val schema = this.javaClass.getResource("/schema.sql").readText()
            session.update(schema)
        }
    }
})
```

