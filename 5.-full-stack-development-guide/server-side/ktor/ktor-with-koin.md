# Ktor with Koin

By default the Ktor module uses Guice for dependency injection. You can use [Koin](https://insert-koin.io/) instead by using `kvision-server-ktor-koin` module.

Koin doesn't support any implicit or just-in-time bindings. All components need to be declared inside modules using Koin DSL. You need to pass all your modules to the `kvisionInit()` function.

```kotlin
fun Application.main() {
    
    // other configuration
    
    val module = module {
        factoryOf(::AddressService)
        factoryOf(::ProfileService)
        singleOf(::RegisterProfileService)
    }
    kvisionInit(module)
}

```

KVision works best with constructor DSL from Koin 3.2. You should use `factoryOf()` binding function for all components that need access to the Ktor's `ApplicationCall` instance and use constructor parameter injection.

```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class AddressService(private val call: ApplicationCall) : IAddressService {
    // methods implementation
}
```

&#x20;You can use lazy evaluated injection or other scopes (e.g. single) for services that do not need to work with `ApplicationCall`.
