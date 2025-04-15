# Exception Handling

When the backend code throws an unhandled exception, it will be logged on the server side and then propagated by KVision to the frontend side. There are of course many different classes of exceptions on the JVM and it's not possible to transform those types directly to the JS side. So only the exception message is propagated and the generic `Exception` object is created on the frontend side.&#x20;

There is a special `io.kvision.remote.ServiceException` class defined in common KVision code. Unlike all other exception classes this one will be propagated directly from `ServiceException` to `ServiceException` and will not be logged on the backend side. It should be used as typical business error indication.

{% code title="Backend.kt" %}
```kotlin
@Suppress("ACTUAL_WITHOUT_EXPECT")
actual class PasswordService : IPasswordService {
    override suspend fun changePassword(oldPassword: String, newPassword: String) {
        if (oldPassword == newPassword) {
            throw ServiceException("You should really change your password")
        }
        // ...
    }
}
```
{% endcode %}

{% code title="Frontend.kt" %}
```kotlin
val passwordService = PasswordService()
try {
    passwordService.changePassword(oldPassword, newPassword)
} catch (e: ServiceException) {
    Alert.show("Error", e.message)
}
```
{% endcode %}

## User-defined exceptions

Since KVision 5.8.2 it is possible to define custom exception types in the common module, that will be propagated from the backend to the fronted side. Such exceptions need to inherit from `AbstractServiceException` and need to be annotated with `@KVServiceException` annotation.

```kotlin
@KVServiceException
class MyFirstException(override val message: String) : AbstractServiceException()

@KVServiceException
class MySecondException(override val message: String) : AbstractServiceException()
```

Such exceptions are automatically registered by the framework and you should be able to throw custom exceptions on the backend side and catch them on the frontend side.

## Returning Result\<T>

When returning `Result<T>` from the remote methods, the exceptions wrapped in the `Result` class are serialized and deserialized the same way as when being thrown. So you can safely use `ServiceException` and other user-defined exceptions when working with `Result<T>` on both the frontend and the backend side of your application.
