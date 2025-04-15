# Vert.x

[Eclipse Vert.x](https://vertx.io) is a toolkit for building reactive applications on the JVM. It's event driven, non-blocking, lightweight and fast.

## Build configuration

The integration with Vert.x is contained in the `kvision-server-vertx` module. It has to be added as the dependency in the common target. This module depends on the `vertx-web`, `vertx-lang-kotlin`, `vertx-lang-kotlin-coroutines`, `jackson-module-kotlin` and `guice` libraries. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation(kotlin("reflect"))
    implementation("ch.qos.logback:logback-classic:$logbackVersion")
    implementation("io.vertx:vertx-auth-common:$vertxVersion")
    implementation("com.h2database:h2:$h2Version")
    implementation("org.jetbrains.exposed:exposed:$exposedVersion")
    implementation("org.postgresql:postgresql:$pgsqlVersion")
    implementation("com.zaxxer:HikariCP:$hikariVersion")
    implementation("commons-codec:commons-codec:$commonsCodecVersion")
    implementation("com.axiomalaska:jdbc-named-parameters:$jdbcNamedParametersVersion")
    implementation("com.github.andrewoma.kwery:core:$kweryVersion")
    implementation("net.jmob:guice.conf:$guiceConfVersion")
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

The integration module utilizes [Guice](https://github.com/google/guice) and you can access external components and resources by injecting object instances into your class. KVision allows you to inject `RoutingContext` instance, which gives you access to the `Vertx` instance and also allows you to access the current request and session information.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    
    @Inject
    lateinit var rctx: RoutingContext

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(rctx.request().remoteAddress().host())
        println(rctx.session().id())
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

Since Vert.x architecture is asynchronous and non-blocking, you should **never** block a thread in your application code. If you have to use some blocking code (e.g. blocking I/O, JDBC) always use the dedicated coroutine dispatcher.

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

### The main verticle

Vert.x services are deployed as "verticles". Main verticle is the application starting point. It's used to initialize and configure the application. Minimal implementation for KVision integration contains `kvisionInit` and `applyRoutes` function calls.

```kotlin
import io.vertx.core.AbstractVerticle
import io.vertx.ext.web.Router
import io.kvision.remote.applyRoutes
import io.kvision.remote.getServiceManager
import io.kvision.remote.kvisionInit

class MainVerticle : AbstractVerticle() {
    override fun start() {
        val router = Router.router(vertx)
        vertx.kvisionInit(router)
        vertx.applyRoutes(router, getServiceManager<IPingService>())
        vertx.createHttpServer().requestHandler(router).listen(8080)
    }
}
```

If you are using websockets, you need to use a special version of `kvisionInit` function, and pass a `HttpServer` instance and a list of all services declaring websocket channel connections.

```kotlin
class MainVerticle : AbstractVerticle() {
    override fun start() {
        val router = Router.router(vertx)
        val server = vertx.createHttpServer()
        vertx.kvisionInit(router, server, listOf(getServiceManager<ITweetService>()))
        server.requestHandler(router).listen(8080)
    }
}
```

### Authentication

You can use standard Vert.x configuration to add authentication and authorization to your application. You can use `serviceRoute` extension function to apply your `AuthHandler` to the selected KVision services.

```kotlin
import io.kvision.remote.serviceRoute

const val SESSION_PROFILE_KEY = "com.example.profile"

class MainVerticle : AbstractVerticle() {
    override fun start() {
        val router = Router.router(vertx)
        vertx.kvisionInit(router, ConfigModule(), DbModule())
        router.route().handler(SessionHandler.create(LocalSessionStore.create(vertx)))
        val authHandler = MyAuthHandler()
        router.serviceRoute(AddressServiceManager, authHandler)
        router.serviceRoute(ProfileServiceManager, authHandler)
        vertx.applyRoutes(router, getServiceManager<IAddressService>())
        vertx.applyRoutes(router, getServiceManager<IProfileService>())
        vertx.applyRoutes(router, getServiceManager<IRegisterProfileService>())
        router.route(HttpMethod.POST, "/login").handler(BodyHandler.create(false)).blockingHandler { rctx ->
            val username = rctx.request().getFormAttribute("username") ?: ""
            val password = rctx.request().getFormAttribute("password") ?: ""
            transaction {
                UserDao.select {
                    (UserDao.username eq username) and (UserDao.password eq DigestUtils.sha256Hex(password))
                }.firstOrNull()?.let {
                    val profile =
                        Profile(it[UserDao.id], it[UserDao.name], it[UserDao.username].toString(), null, null)
                    rctx.session().put(SESSION_PROFILE_KEY, profile)
                    rctx.response().setStatusCode(200).end()
                } ?: rctx.response().setStatusCode(401).end()
            }
        }
        router.route(HttpMethod.GET, "/logout").handler { rctx ->
            rctx.clearUser()
            rctx.session().destroy()
            rctx.response().putHeader("Location", "/").setStatusCode(302).end()
        }
        vertx.createHttpServer().requestHandler(router).listen(8080)
    }
}
```
