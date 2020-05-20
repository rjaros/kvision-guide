# Spring Boot

[Spring Boot](https://spring.io/projects/spring-boot) is an open source Java-based framework made to ease the bootstrapping and development of the Spring applications. It is developed by Pivotal, and is a part of the Spring Framework. It gives you easy access to rich Spring ecosystem, with many enterprise-grade libraries and technologies. Spring Boot applications can be written in Kotlin and the language is officially supported by the framework.

## Build configuration

The integration with Spring Boot is contained in the `kvision-server-spring-boot` module. It has to be added as the dependency in the server target. This module depends on the `spring-boot-starter`, `spring-boot-starter-web`, `jackson-module-kotlin` and `pac4j-core` libraries. Any other dependencies can be added to `build.gradle.kts` and then be used in your application.

{% code-tabs %}
{% code-tabs-item title="build.gradle.kts" %}
```kotlin
dependencies {
    implementation(kotlin("stdlib-jdk8"))
    implementation(kotlin("reflect"))
    implementation("pl.treksoft:kvision-server-spring-boot:$kvisionVersion")
    implementation("org.springframework.boot:spring-boot-starter")
    implementation("org.springframework.boot:spring-boot-devtools")
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.springframework.boot:spring-boot-starter-jdbc")
    implementation("org.pac4j:spring-webmvc-pac4j:$springMvcPac4jVersion")
    implementation("org.pac4j:pac4j-http:$pac4jVersion")
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

## Application configuration

The standard way to configure Spring Boot application is `src/backendMain/resources/application.yml` file. It contains options needed for optional dependencies. It can be empty if they are not used.

## Implementation

### Service class

The implementation of the service class comes down to implementing required interface methods and making it a Spring component by using a `@Service` annotation with a "prototype" scope. 

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
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

Spring IoC \(Inversion of Control\) allows you to inject resources and other Spring components into your service class. You can use standard Spring `@Autowired` annotation. In particular you can inject `Environment`, `ServletContext`, `HttpServletRequest` and `HttpSession` objects.

```kotlin
@Service
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
actual class AddressService : IAddressService {
    @Autowired
    lateinit var env: Environment
    @Autowired
    lateinit var servletContext: ServletContext
    @Autowired
    lateinit var request: HttpServletRequest
    @Autowired
    lateinit var session: HttpSession

    override suspend fun getAddressList(search: String?, sort: Sort) {
        println(env.getProperty("option1", "default"))
        println(request.requestURI)
        println(session.id)
        println(servletContext.attributeNames)
        return listOf()
    }
    // ...
}
```

You can also inject custom Spring components, defined throughout your application.

{% hint style="info" %}
Note: The new instance of the service class will be created by Spring for every server request. Use servlet context, session or request objects to store your state with appropriate scope.
{% endhint %}

### Application class

To allow KVision work with Spring Boot you have to pass all instances of the `KVServiceManager` objects \(defined in common code\) to the Spring environment. You do this by defining a provider method for the `List<KVServiceManager<Any>>` instance in the main application class.

```kotlin
import org.springframework.boot.autoconfigure.SpringBootApplication
import org.springframework.boot.runApplication
import org.springframework.context.annotation.Bean

@SpringBootApplication
class KVApplication {
    @Bean
    fun getManagers() = listOf(AddressServiceManager)
}

fun main(args: Array<String>) {
    runApplication<KVApplication>(*args)
}
```

### Authentication with Pac4j

KVision is already integrated with [Pac4j](https://www.pac4j.org/) security engine and you can use the `Profile` class in common, frontend and backend code. The configuration below allows you to use [Spring WebMVC Pac4j](https://github.com/pac4j/spring-webmvc-pac4j) project with your KVision application. By using `addPathPatternsFromServices` extension function you can enable authentication for selected services.

```kotlin
@Service
class MyDbProfileService constructor(ds: DataSource) :
    DbProfileService(ds, SpringSecurityPasswordEncoder(BCryptPasswordEncoder()))

@Configuration
class Pac4jConfig {
    @Autowired
    lateinit var myDbProfileService: MyDbProfileService
    
    @Bean
    fun config(): Config {
        val formClient = FormClient("/") { credentials, context ->
            myDbProfileService.validate(credentials as UsernamePasswordCredentials, context)
        }
        val clients = Clients("http://localhost:8080/callback", formClient)
        val config = Config(clients)
        return config
    }
}

@Configuration
@ComponentScan(basePackages = arrayOf("org.pac4j.springframework.web"))
class SecurityConfig : WebMvcConfigurer {
    @Autowired
    private val config: Config? = null

    override fun addInterceptors(registry: InterceptorRegistry) {
        registry.addInterceptor(SecurityInterceptor(config, "FormClient"))
            .addPathPatternsFromServices(listOf(AddressServiceManager, ProfileServiceManager))
    }
}
```

