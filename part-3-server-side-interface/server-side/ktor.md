# Ktor

[Ktor](https://ktor.io/) is the asynchronous web framework for Kotlin created by JetBrains. It is written in Kotlin and its machinery and API is utilizing Kotlin coroutines. It's unopinionated design makes it a very good choice for development of applications composed of many different technologies. Since both Ktor and KVision are based on coroutines, the plugging of KVision's server module into Ktor's pipeline is executed in a natural and efficient way.

## Build configuration

The integration with Ktor is contained in the kvision-server-ktor module. It has to be added as the dependency in the server subproject. This module depends on the `ktor-server-core`, `ktor-jackson`, `jackson-module-kotlin` and `guice` libraries. Any other dependencies can be added to build.gradle and then be used in your application.

{% code-tabs %}
{% code-tabs-item title="build.gradle" %}
```groovy
apply plugin: 'kotlin-platform-jvm'
apply plugin: 'kotlinx-serialization'
apply plugin: 'application'
apply plugin: "com.github.johnrengelman.shadow"

mainClassName = 'io.ktor.server.netty.EngineMain'

dependencies {
    expectedBy project(':common')

    compile "org.jetbrains.kotlin:kotlin-stdlib-jdk8:${kotlinVersion}"
    compile "org.jetbrains.kotlin:kotlin-reflect:${kotlinVersion}"
    compile "pl.treksoft:kvision-server-ktor:${kvisionVersion}"
    compile "io.ktor:ktor-server-netty:${ktorVersion}"
    compile "io.ktor:ktor-auth:${ktorVersion}"
    compile "ch.qos.logback:logback-classic:${logbackVersion}"
    compile "com.h2database:h2:${h2Version}"
    compile "org.postgresql:postgresql:${pgsqlVersion}"
    compile "org.jetbrains.exposed:exposed:${exposedVersion}"
    compile "com.zaxxer:HikariCP:${hikariVersion}"
}

sourceSets.main.resources {
    srcDirs = ["conf", "public"]
}

sourceSets.main.java {
    srcDirs "../common/src/main/kotlin"
}

compileKotlin {
    targetCompatibility = javaVersion
    sourceCompatibility = javaVersion
    kotlinOptions {
        jvmTarget = javaVersion
    }
}

shadowJar {
    manifest {
        attributes 'Main-Class': mainClassName
    }
    into('/assets') {
        from fileTree('../client/build/distributions/client')
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
Note the assets location defined inside the `shadowJar` task. It contains the compiled production code of the client application.
{% endhint %}

{% hint style="info" %}
Note: You can use other engines instead of Netty - see [Ktor documentation](https://ktor.io/servers/configuration#embedded-server). Remember to add the appropriate dependency.
{% endhint %}

## Application configuration

The standard way to configure Ktor application is `conf/application.conf` file. Among other options it contains the name of the main function of your app.

{% code-tabs %}
{% code-tabs-item title="application.conf" %}
```text
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
{% endcode-tabs-item %}
{% endcode-tabs %}

## Implementation

### Service class

The implementation of the simple service class comes down to implementing required interface methods.

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

Since the service interface is strictly bound to the definition used in the common and client modules, the best way to access additional information inside its implementation methods is the dependency injection. The integration module utilizes [Guice](https://github.com/google/guice) and you can access external information and resources by injecting server objects into your class. By default KVision allows you to inject `Application` and `ApplicationCall` instances. These objects give you access to the application configuration, its state, the current request and the user session \(if it is configured\).

```kotlin
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

Since Ktor architecture is asynchronous and non-blocking, you should **never** block a thread in your application code. If you have to use some blocking code \(e.g. blocking I/O, JDBC\) always use the dedicated coroutine dispatcher.

```kotlin
actual class AddressService : IAddressService {
    override suspend fun getAddressList(search: String?, sort: Sort) {
        return withContext(Dispatchers.IO) {
            retrieveAddressesFromDatabase(search, sort)
        }
    }
}
```

### Main function

This function is the application starting point. It's used to initialize and configure application modules and features. Minimal implementation for KVision integration contains `kvisionInit` and `applyRoutes` function calls.

```kotlin
import io.ktor.application.Application
import io.ktor.routing.routing
import pl.treksoft.kvision.remote.applyRoutes
import pl.treksoft.kvision.remote.kvisionInit

fun Application.main() {
    kvisionInit()
    routing {
        applyRoutes(AddressServiceManager)
    }
}
```

The `kvisionInit` function can take multiple parameters of type `com.google.inject.Module`. It allows you to register custom modules inside the main Guice injector.

```kotlin
fun Application.main() {
    kvisionInit(HelloModule())
    routing {
        applyRoutes(AddressServiceManager)
    }
}

class HelloModule() : AbstractModule() {
    override fun configure() {}

    @Provides
    @Singleton
    @Named("hello-message")
    fun helloMessage(): String = "Hello from Guice"
}
```

Apart from the above KVision configuration you are free to use any other module or feature of the Ktor framework. When using [authentication feature](https://ktor.io/servers/features/authentication.html) you can choose where to apply KVision routes for different services.

```kotlin
fun Application.main() {
    install(DefaultHeaders)
    install(CallLogging)
    install(Sessions) {
        cookie<Profile>("SESSION", storage = SessionStorageMemory()) {
            cookie.path = "/"
            cookie.extensions["SameSite"] = "strict"
        }
    }
    kvisionInit()
    Db.init(environment.config)

    install(Authentication) {
        form {
            userParamName = "username"
            passwordParamName = "password"
            challenge = FormAuthChallenge.Unauthorized
            validate { 
                // ...
            }
            skipWhen { call -> call.sessions.get<Profile>() != null }
        }
    }

    routing {
        applyRoutes(RegisterProfileServiceManager) // No authentication needed
        authenticate {
            post("login") {
                // ...
            }
            get("logout") {
                call.sessions.clear<Profile>()
                call.respondRedirect("/")
            }
            applyRoutes(AddressServiceManager) // Authentication needed
            applyRoutes(ProfileServiceManager) // Authentication needed
        }
    }
}
```

