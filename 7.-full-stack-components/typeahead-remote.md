# Tom Typeahead Remote

The `io.kvision.form.text.TomTypeaheadRemoteInput` component, contained in `kvision-tom-select-remote` module, is a special component you can use to render a typeahead text field with values loaded from the server. Unlike standard `TomTypeaheadInput` component (which can also load values from an AJAX source but needs a defined endpoint) `TomTypeaheadRemoteInput` is bound directly to the method of the remote service. The method signature looks like this:

```kotlin
@KVService
interface IValueService {
    suspend fun values(search: String?, state: String?): List<String>
}
```

The `search` parameter is send with the value entered by the user in the text field. It should be used to filter the returned list of values.

The `state` parameter allows you to send optional, additional data to the backend service, with the help of the `stateFunction` parameter of the `TomTypeaheadRemoteInput` constructor.

To use `TomTypeaheadRemote` form control, you initialize it with the `ServiceManager` instance and a callable reference to the right method.&#x20;

```kotlin
TomTypeaheadRemote(serviceManager = ValueServiceManager, 
    function = IValueService::values,
    stateFunction = { someState.toString() },
    label = "Start typing to see appropriate values"
)
```
