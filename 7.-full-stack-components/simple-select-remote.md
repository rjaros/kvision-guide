# Select Remote

The `io.kvision.form.select.SelectRemoteInput` component, contained in `kvision-select-remote` module, is a special component you can use to render a select box with options loaded from the server.  `SelectRemoteInput` is bound directly to the method of the remote service. The method signature looks like this:

```kotlin
@KVService
interface IDictionaryService {
    suspend fun dictionary(state: String?): List<SimpleRemoteOption>
}
```

The `state` parameter allows you to send optional, additional data to the backend service, with the help of the `stateFunction` parameter of the `SelectRemoteInput` constructor.

The `SimpleRemoteOption` class is defined as:

```kotlin
@Serializable
data class SimpleRemoteOption(
    val value: String,
    val text: String? = null
)
```

and allows to send value and label for every option.

To use `SelectRemote` form control, you initialize it with the `ServiceManager` instance and a callable reference to the right method.&#x20;

```kotlin
SelectRemote(serviceManager = DictionaryServiceManager, 
    function = IDictionaryService::dictionary,
    stateFunction = { someState.toString() },
    label = "Select option from the dictionary"
)
```
