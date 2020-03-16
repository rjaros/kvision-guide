# Javalin

[Javalin](https://javalin.io) is a very popular, simple, lightweight and flexible micro web framework for Java and Kotlin. It's un-opinionated and runs on top of Jetty, one of the most used and stable web-servers for the JVM.

## Build configuration

The integration with Javalin is contained in the `kvision-server-javalin` module. It has to be added as the dependency in the common target. This module depends on the`jackson-module-kotlin` and `guice` libraries. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation(kotlin("reflect"))
    implementation("org.slf4j:slf4j-simple:$slf4jVersion")
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

The integration module utilizes [Guice](https://github.com/google/guice) and you can access external components and resources by injecting object instances into your class. KVision allows you to inject `Javalin` instance.

```kotlin
actual class AddressService : IAddressService {
    
    @Inject
    lateinit var javalin: Javalin

    override suspend fun getAddressList(search: String?, sort: Sort) {
        return listOf()
    }
    // ...
}
```

You can use special KVision interfaces to get access to `Context` and `HttpSession` objects.

```kotlin
interface WithContext {
    var ctx: Context
}

interface WithHttpSession {
    var httpSession: HttpSession
}
```

Just implement any of these interfaces when you are defining you service class, and KVision will automatically assign the corresponding object for you.

```kotlin
actual class AddressService : IAddressService, WithContext, WithHttpSession {
    
    override lateinit var ctx: Context
    override lateinit var httpSession: HttpSession

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(ctx.req.remoteAddr)
        println(httpSession.id)
        return listOf()
    }
    // ...
}
```

You can also inject other Guice components, defined in your application and configured in custom Guice modules or with [just-in-time bindings](https://github.com/google/guice/wiki/JustInTimeBindings).

{% hint style="info" %}
Note: The new instance of the service class will be created by Guice for every server request. Use session or request objects to store your state with appropriate scope.
{% endhint %}

### Application class

This class is the application starting point. It's used to initialize and configure the application. Minimal implementation for KVision integration contains `kvisionInit` and `applyRoutes` function calls.

```kotlin
import io.javalin.Javalin
import pl.treksoft.kvision.remote.applyRoutes
import pl.treksoft.kvision.remote.kvisionInit

fun main() {
    Javalin.create().start(8080).apply {
        kvisionInit()
        applyRoutes(AddressServiceManager)
    }
}
```

### Authentication

When configuring security with `AccessManager` you can call `applyRoutes` with additional parameter containing roles.

```kotlin
enum class ApiRole : Role { AUTHORIZED, ANYONE }

fun main() {
    Javalin.create { config ->
        config.accessManager { handler, ctx, permittedRoles ->
            when {
                permittedRoles.contains(ApiRole.ANYONE) -> handler.handle(ctx)
                ctx.sessionAttribute<Profile>(SESSION_PROFILE_KEY) != null -> handler.handle(ctx)
                else -> ctx.status(HttpServletResponse.SC_UNAUTHORIZED).json("Unauthorized")
            }
        }
    }.start(8080).apply {
        kvisionInit(ConfigModule(), DbModule())
        applyRoutes(AddressServiceManager, setOf(ApiRole.AUTHORIZED))
        applyRoutes(ProfileServiceManager, setOf(ApiRole.AUTHORIZED))
        applyRoutes(RegisterProfileServiceManager, setOf(ApiRole.ANYONE))

        // Security config
        post("/login", { ctx ->
            val username = ctx.formParam("username") ?: ""
            val password = ctx.formParam("password") ?: ""
            transaction {
                UserDao.select {
                    (UserDao.username eq username) and (UserDao.password eq DigestUtils.sha256Hex(password))
                }.firstOrNull()?.let {
                    val profile =
                        Profile(it[UserDao.id], it[UserDao.name], it[UserDao.username].toString(), null, null)
                    ctx.sessionAttribute(SESSION_PROFILE_KEY, profile)
                    ctx.status(HttpServletResponse.SC_OK)
                } ?: ctx.status(HttpServletResponse.SC_UNAUTHORIZED)
            }
        }, setOf(ApiRole.ANYONE))
        get("/logout", { ctx ->
            ctx.req.remoteAddr
            ctx.req.session.id
            ctx.redirect("/", HttpServletResponse.SC_FOUND)
        }, setOf(ApiRole.AUTHORIZED))
    }
}
```

