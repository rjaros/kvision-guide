# Tom Select Remote

The `io.kvision.form.select.TomSelectRemoteInput` component, contained in `kvision-tom-select-remote` module, is a special component you can use to render a select box with options loaded from the server. Unlike standard `TomSelectInput` component (which can also load options from an AJAX source but needs a defined endpoint) `TomSelectRemoteInput` is bound directly to the method of the remote service. The method signature looks like this:

```kotlin
@KVService
interface IDictionaryService {
    suspend fun dictionary(search: String?, initial: String?, state: String?): List<RemoteOption>
}
```

The `search` parameter is send with the value entered by the user into the search box of the select control. It should be used to filter the returned list of options.

&#x20;The `initial` parameter is send with the initial value of the select control. If it's not null, the option bound to the given value should be returned (even if `search` is not null and does not match the initial value).

The `state` parameter allows you to send optional, additional data to the backend service, with the help of the `stateFunction` parameter of the `TomSelectRemoteInput` constructor.

The `RemoteOption` class is defined as:

```kotlin
@Serializable
data class RemoteOption(
    val value: String? = null,
    val text: String? = null,
    val className: String? = null,
    val subtext: String? = null,
    val icon: String? = null,
    val content: String? = null,
    val disabled: Boolean = false,
    val divider: Boolean = false
)
```

and allows to send full set of properties for every option.

To use `TomSelectRemote` form control, you initialize it with the `ServiceManager` instance and a callable reference to the right method.&#x20;

```kotlin
TomSelectRemote(serviceManager = DictionaryServiceManager, 
    function = IDictionaryService::dictionary,
    stateFunction = { someState.toString() },
    label = "Select option from the dictionary"
)
```

