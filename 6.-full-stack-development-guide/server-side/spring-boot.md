# Spring Boot

[Spring Boot](https://spring.io/projects/spring-boot) is an open source Java-based framework made to ease the bootstrapping and development of the Spring applications. It is developed by Pivotal, and is a part of the Spring Framework. It gives you easy access to rich Spring ecosystem, with many enterprise-grade libraries and technologies. Spring Boot applications can be written in Kotlin and the language is officially supported by the framework.

## Build configuration

The integration with Spring Boot is contained in the `kvision-server-spring-boot` module. It has to be added as the dependency in the common target. This module depends on the `spring-boot-starter`, `spring-boot-starter-webflux`, `spring-boot-starter-security` and `spring-data-relational`. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation(kotlin("reflect"))
    implementation("org.springframework.boot:spring-boot-starter")
    implementation("org.springframework.boot:spring-boot-devtools")
    implementation("org.springframework.boot:spring-boot-starter-webflux")
    implementation("org.springframework.boot:spring-boot-starter-security")
    implementation("org.springframework.boot.experimental:spring-boot-actuator-autoconfigure-r2dbc:$springAutoconfigureR2dbcVersion")
    implementation("org.springframework.data:spring-data-r2dbc:$springDataR2dbcVersion")
    implementation("io.r2dbc:r2dbc-postgresql:$r2dbcPostgresqlVersion")
    implementation("io.r2dbc:r2dbc-h2:$r2dbcH2Version")
}
```
{% endcode %}

## Application configuration

The standard way to configure Spring Boot application is `src/jvmMain/resources/application.yml` file. It contains options needed for optional dependencies. It can be empty if they are not used.

## Implementation

### Service class

The implementation of the service class comes down to implementing required interface methods and making it a Spring component by using a `@Service` annotation with a "prototype" scope.&#x20;

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
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

Spring IoC (Inversion of Control) allows you to inject resources and other Spring components into your service class. You can use standard Spring `@Autowired` annotation or constructor based injection.

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {
    @Autowired
    lateinit var env: Environment

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(env.getProperty("option1", "default"))
        return listOf()
    }
    // ...
}
```

You can also inject custom Spring components, defined throughout your application.

KVision allows you to inject `ServerRequest` object, which gives you access to the user session as well.

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService : IAddressService {

    @Autowired
    lateinit var serverRequest: ServerRequest

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(serverRequest.uri())
        println(serverRequest.session().awaitSingle().id)
        return listOf()
    }
    // ...
}
```

{% hint style="info" %}
Note: The new instance of the service class will be created by Spring for every server request. Use session or request objects to store your state with appropriate scope.
{% endhint %}

### **Blocking code**

Since Spring WebFlux architecture is asynchronous and non-blocking, you should not block threads in your application code. If you have to use some blocking code (e.g. blocking I/O, JDBC) use the dedicated coroutine dispatcher.

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
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

To allow KVision work with Spring Boot you have to pass all instances of the `KVServiceManager` objects (defined in common code) to the Spring environment. You do this by defining a provider method for the `List<KVServiceManager<Any>>` instance in the main application class.

{% hint style="info" %}
Use `@EnableAutoConfiguration` annotation to disable security if your app doesn't need it.
{% endhint %}

```kotlin
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.context.annotation.Bean

@SpringBootApplication
@EnableAutoConfiguration(
    exclude = [
        org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration::class,
        org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration::class
    ]
)
class KVApplication {
    @Bean
    fun getManagers() = listOf(getServiceManager<IAddressService>())
}

fun main(args: Array<String>) {
    runApplication<KVApplication>(*args)
}
```

### Authentication

You can use standard Spring WebFlux Security configuration, and with a help of `serviceMatchers` extension function, you can automatically select endpoints that should be secured.

```kotlin
@EnableWebFluxSecurity
@Configuration
class SecurityConfiguration {

    @Bean
    fun securityWebFilterChain(http: ServerHttpSecurity): SecurityWebFilterChain {
        return http.authorizeExchange {
            it.serviceMatchers(getServiceManager<IAddressService>(), getServiceManager<IProfileService>())
                .authenticated().pathMatchers("/**").permitAll()
        }.csrf {
            it.disable()
        }.exceptionHandling {
            it.authenticationEntryPoint { exchange, _ ->
                val response = exchange.response
                response.statusCode = HttpStatus.UNAUTHORIZED
                exchange.mutate().response(response)
                Mono.empty()
            }
        }.formLogin {
            it.loginPage("/login")
                .authenticationSuccessHandler(RedirectServerAuthenticationSuccessHandler().apply {
                    this.setRedirectStrategy { exchange, _ ->
                        Mono.fromRunnable {
                            val response = exchange.response
                            response.statusCode = HttpStatus.OK
                        }
                    }
                }).authenticationFailureHandler(RedirectServerAuthenticationFailureHandler("/login").apply {
                    this.setRedirectStrategy { exchange, _ ->
                        Mono.fromRunnable {
                            val response = exchange.response
                            response.statusCode = HttpStatus.UNAUTHORIZED
                        }
                    }
                })
        }.logout {
            it.logoutUrl("/logout")
                .requiresLogout(ServerWebExchangeMatchers.pathMatchers(HttpMethod.GET, "/logout"))
                .logoutSuccessHandler(RedirectServerLogoutSuccessHandler().apply {
                    setLogoutSuccessUrl(URI.create("/"))
                })
        }.build()
    }
}
```
