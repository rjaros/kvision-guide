# Exception handling

When the backend code throws an unhandled exception, it will be logged on the server side and then propagated by KVision to the frontend side. There are of course many different classes of exceptions on the JVM and it's not possible to transform those types directly to the JS side. So only the exception message is propagated and the generic `Exception` object is created on the frontend side. 

There is a special `pl.treksoft.remote.ServiceException` class defined in common KVision code. Unlike all other exception classes this one will be propagated directly from `ServiceException` to `ServiceException` and will not be logged on the backend side. It should be used as typical business error indication.

{% code title="Backend.kt" %}
```kotlin
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

