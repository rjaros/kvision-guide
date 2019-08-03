# Remote select

The `pl.treksoft.kvision.form.select.SelectRemoteInput` component, contained in `kvision-select-remote` module, is a special component you can use to render a select box with options loaded from the server. Unlike standard `SelectInput` component \(which can also load options from an AJAX source but needs a defined endpoint\) `SelectRemoteInput` is bound directly to the method of the remote service. The method signature looks like this:

```kotlin
interface IDictionaryService {
    fun dictionary(search: String?, initial: String?): List<RemoteOption>
}
```

{% hint style="info" %}
Note: This method doesn't need to be suspending, because it is not called directly on the client side.
{% endhint %}

The `search` parameter is send with the value entered by the user into the search box of the select control. It should be used to filter the returned list of options.

 The `initial` parameter is send with the initial value of the select control. If it's not null, the option bound to the given value should be returned \(even if `search` is not null and does not match the initial value\).

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

To use `SelectRemote` form control, you initialize it with the `ServiceManager` instance and a callable reference to the right method. 

```kotlin
SelectRemote(serviceManager = DictionaryServiceManager, 
    function = IDictionaryService::dictionary,
    label = "Select option from the dictionary"
)
```



