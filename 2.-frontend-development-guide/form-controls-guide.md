# Form controls

All KVision form controls are available in two variants. The first variant, with class name ending with "Input" (e.g. `TextInput`), is a basic, fully-functional component ready to be used as a standalone widget. The second variant, with class name without "Input" (e.g. `Text`), is a wrapper component implementing one of `FormControl` interfaces and is intended primarily for use with a `FormPanel` container. This variant can display a label, a validation information and has some additional CSS styling consistent with Bootstrap forms components. From the developer point of view both variants have the same properties and methods (a side of `label` and `rich` properties available for second variant only).&#x20;

{% hint style="info" %}
The following reference do not differentiate both variants of form controls.&#x20;
{% endhint %}

All KVision form controls have the following properties:

* `value` - allows to get or set the value of the component
* `name` - defines a `name` attribute of the HTML input element
* `size` - allows to set the size of the form control (normal, large or small Bootstrap style)
* `disabled` - defines whether the form control is disabled

## Text fields

### `i.k.f.text.Text`

This component is rendered as a standard HTML text field and is used to get simple, textual data from the user. The `type` property can be set to any of these values: `TEXT`, `PASSWORD`, `EMAIL`, `TEL`, `COLOR`, `SEARCH`, `URL` and is bound to the `type` attribute of the HTML input field. Modern browsers can render such fields with additional elements (e.g. color chooser). Other properties allow you to define a placeholder, a maximum length, autofocus or autocomplete attributes and define if the control should be read-only. &#x20;

```kotlin
Text(type = TextInputType.URL, label = "WWW") {
    placeholder = "Enter the address"
    maxlength = 255
    autofocus = true
    autocomplete = false
    readonly = false
}
```

With an additional `kvision-imask` module loaded and initialized you can use all text input mask options supported by [Imask.js](https://imask.js.org) library.

```kotlin
text(label = "Phone number") {
    maskOptions = ImaskOptions(pattern = PatternMask("+{7}(000)000-00-00", lazy = false, eager = true))
}
```

### `i.k.f.text.Password`

This component is a direct subclass of `i.k.form.text.Text` with `type` property set to `TextInputType.PASSWORD`.

```kotlin
Password(label = "Password")
```

### `i.k.f.text.TextArea`

This component is rendered as a standard HTML textarea field and is used to get longer, multi-line textual data from the user. It can be sized with `cols` and `rows` properties (mapped to standard HTML attributes).

```kotlin
TextArea(cols = 100, rows = 5, label = "Enter some text")
```

&#x20;Alternative way (and preferred nowadays) is to set CSS `width` and `height` properties.

```kotlin
TextArea(label = "Enter some text") {
    width = 300.px
    height = 120.px
}
```

Other properties allow you to define a placeholder, a maximum length, autofocus or hard wrap attributes and define if the control should be read-only.

```kotlin
TextArea(label = "Enter some text") {
    width = 300.px
    height = 120.px
    placeholder = "Enter some text"
    autofocus = true
    wrapHard = true
    readonly = true
}
```

### `i.k.f.text.RichText`

The kvision-richtext module allows you to use a dedicated form control based on a modern [Trix Editor](https://trix-editor.org/) component. It can be used to get rich text from the user. This component renders a text editing field with a toolbar containing basic formatting options (bold, italic, strikethrough, heading, quote, code, link and lists with indentations). The `value` property contains properly formatted HTML markup. Other properties allow you to define a placeholder and autofocus attribute.

```kotlin
RichText(
    value = "<b>Bold text</b>",
    label = "Rich text field with a placeholder"
) {
    placeholder = "Enter some text"
    autofocus = true
}
```

### `i.k.f.text.TomTypeahead`

The kvision-tom-select module contains an enhanced text field with support for typeahead (autocomplete) functionality. Both local and remote data sources are supported. To use local data source pass a list of values with the `options` parameter.

```kotlin
TomTypeahead(
    options = listOf("Alabama", "Alaska", "Arizona", "Arkansas", "California"),
    label = "Select state"
)
```

To use a remote data source pass a `TomSelectCallbacks` object with a `load` callback.

```kotlin
TomTypeahead(
    label = "Select a repository",
    tsCallbacks = TomSelectCallbacks(
        load = { query, callback ->
            restClient.callDynamic("https://api.github.com/search/repositories") {
                data = obj { q = query }
                resultTransform = { it.items }
            }.then { items: dynamic ->
                @Suppress("UnsafeCastFromDynamic")
                callback(items.map { item -> item.name })
            }
        }
    )
)
```

### `i.k.f.text.TomTypeaheadRemote`

This component is contained in the `kvision-bootstrap-typeahead-remote` module and is a special version of the `Typeahead` control, tailored for use with KVision server side interfaces. You can find more information in [part 3](../7.-full-stack-components/typeahead-remote.md) of this guide.

## Checkboxes and radiobuttons

### `i.k.f.check.CheckBox`

This component can be used to display a checkbox and to get true/false data from the user. The `style` property can be used to render the checkbox with one of six different styles.

```kotlin
vPanel {
    checkBox(true, label = "Default checkbox") { style = CheckBoxStyle.DEFAULT }
    checkBox(true, label = "Primary checkbox") { style = CheckBoxStyle.PRIMARY }
    checkBox(true, label = "Success checkbox") { style = CheckBoxStyle.SUCCESS }
    checkBox(true, label = "Info checkbox") { style = CheckBoxStyle.INFO }
    checkBox(true, label = "Warning checkbox") { style = CheckBoxStyle.WARNING }
    checkBox(true, label = "Danger checkbox") { style = CheckBoxStyle.DANGER }
}
```

You can layout your checkboxes horizontally using an `inline` property.

```kotlin
simplePanel {
    checkBox(true, label = "First") { inline = true }
    checkBox(true, label = "Second") { inline = true }
}
```

And you can even make the checkbox look like a radiobutton.

```kotlin
checkBox(true, label = "Circled checkbox") { circled = true }
```

A `CheckBox` component has a convenient `onClick` method to easily bind some action to the `click` event.

```kotlin
checkBox(label = "Click me").onClick {
    println("Clicked")
}
```

By using `indeterminate` property you can make the checkbox appear as neither selected nor deselected.&#x20;

```kotlin
checkBox(label = "Indeterminate checkbox") {
    indeterminate = true
}
```

### `i.k.f.check.Radio`

This component is similar to the `CheckBox` component, but it is rendered as a radiobutton. By using the `name` property, you can group some radiobuttons together, with only one control selected at a time. &#x20;

```kotlin
vPanel {
    radio(name = "radio", label = "First radiobutton")
    radio(name = "radio", label = "Second radiobutton")
    radio(name = "radio", label = "Third radiobutton")
}
```

The `style` property can be used to render the radiobutton with one of six different styles.

```kotlin
vPanel {
    radio(name = "radio", label = "Default radiobutton") { style = RadioStyle.DEFAULT }
    radio(name = "radio", label = "Primary radiobutton") { style = RadioStyle.PRIMARY }
    radio(name = "radio", label = "Success radiobutton") { style = RadioStyle.SUCCESS }
    radio(name = "radio", label = "Info radiobutton") { style = RadioStyle.INFO }
    radio(name = "radio", label = "Warning radiobutton") { style = RadioStyle.WARNING }
    radio(name = "radio", label = "Danger radiobutton") { style = RadioStyle.DANGER }
}
```

You can layout your radiobuttons horizontally using an `inline` property.

```kotlin
simplePanel {
    radio(label = "First") { inline = true }
    radio(label = "Second") { inline = true }
}
```

And you can even make the radiobutton look like a checkbox.

```kotlin
radio(name = "radio", label = "Squared radiobutton") { squared = true }
```

A `Radio` component has a convenient `onClick` method to easily bind some action to the `click` event.

```kotlin
radio(label = "Click me").onClick {
    println("Clicked")
}
```

### `i.k.f.check.RadioGroup`

Although it is possible to use basic `Radio` components to build and manage radiobutton groups (you would have to check which radio in a group is selected), KVision offers a dedicated `RadioGroup` component, which allows you to treat a group of radiobuttons as a single component returning a String value. It can be initialized with a list of options (key to value pairs) and the selected option can be easily set or get with the `value` property.

```kotlin
val radioGroup = RadioGroup(
    listOf("option1" to "First option", "option2" to "Second option"),
    inline = true, label = "Radio button group"
)
radioGroup.value = "option1"
println(radioGroup.value)
```

### `i.k.f.check.TriStateCheckBox`

This component extends the usage of `indeterminate` property of the standard `CheckBox` component and allows you to easily use all three states of the checkbox - selected (`value = true)`, deselected (`value = false)` and indeterminate (`value = null)`.

```kotlin
val tsc = triStateCheckBox(value = true, label = "Tri-state checkbox")
button("Make indeterminate").onClick {
    tsc.value = null
}
```

## Select boxes

### `i.k.f.select.Select`

This is a simple select box component, based on standard HTML select control. It can be used when there is no need for advanced options of the `Select` component. The `SimpleSelect` component should be initialized with a list of options (key to values pairs).

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option"),
    label = "Simple select"
)
```

### `i.k.f.select.TomSelect`

The `kvision-tom-select` module allows you to use a sophisticated form control based on [Tom Select](https://tom-select.js.org/). It's a full-featured component, configurable with plenty of options. It can be used for a simple select picker with a few static options as well as a searchable, dynamic lists pulled over the network. The `TomSelect` component can be initialized with a list of options (key to values pairs).

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option"),
    label = "Select"
)
```

You can select many options at the same time  with `multiple` property. You can use `maxItems` property to limit the number of selected options.

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option", "third" to "Third option"),
    multiple = true,
    label = "Multiple select"
) {
    maxItems = 2
}
```

Other properties allow to define a placeholder, an autofocus attribute and to automatically generate an empty option (to be able to de-select value).

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option"),
    label = "Styled Select"
) {
    placeholder = "Select a value"
    autofocus = true
    emptyOption = true
}
```

`TomSelect` component can also work with a remote data source - just pass a `TomSelectCallbacks` object with a `load` callback

```kotlin
TomSelect(label = "Select a repository",
    emptyOption = true,
    tsCallbacks = TomSelectCallbacks(
        load = { query, callback ->
            restClient.callDynamic("https://api.github.com/search/repositories") {
                data = obj { q = query }
                resultTransform = { it.items }
            }.then { items: dynamic ->
                @Suppress("UnsafeCastFromDynamic")
                callback(items.map { item ->
                    obj {
                        this.value = item.id
                        this.text = item.name
                        this.subtext = item.owner.login
                    }
                })
            }
        },
        shouldLoad = { it.length >= 3 }
    ),
    tsRenders = TomSelectRenders(option = { item, escape ->
        """
            <div>
                <span class="title">${escape(item.text)}</span>
                <small>(${escape(item.subtext)})</small>
            </div>
        """.trimIndent()
    })
)

```

### `i.k.f.select.TomSelectRemote`

This component is contained in the `kvision-tom-select-remote` module and is a special version of `TomSelect` control, tailored for use with KVision server side interfaces. You can find more information in [part 3](../7.-full-stack-components/remote-select.md) of this guide.

## Others

### `i.k.f.time.DateTime`

Both `kvision-datetime` module allows you to use a sophisticated form control based on [Tempus-dominus](https://getdatepicker.com/) library. Is It's a full-featured component, configurable with plenty of options. It can be used to display date and/or time picker, based on the date format specified with [Fecha library formatting tokens](https://github.com/taylorhakes/fecha#formatting-tokens). The default format is _"YYYY-MM-DD HH:mm"_, which means the control will display date and time picker.

```kotlin
DateTime(format = "YYYY-MM-DD", label = "Date field") {
    placeholder = "Enter date"
}

DateTime(format = "HH:mm", label = "Time field") {
    placeholder = "Enter time"
}

DateTime(label = "Date and time field") {
    placeholder = "Enter date and time"
}
```

Other properties allow you to set days of the week that should be disabled,  visibility of "Clear", "Close" and "Today" buttons and also the increment used to build the hour view (default - 5 minutes).

```kotlin
DateTime(label = "Date and time field") {
    placeholder = "Enter date and time"
    daysOfWeekDisabled = arrayOf(0, 6) // Saturday, Sunday
    showClear = false
    showClose = false
    showTodayButton = false
    stepping = 30
}
```

&#x20;You can also use `minDate`, `maxDate`, `enabledDates` and `disabledDates` properties to control which dates the user is allowed to choose.

### `i.k.f.number.Spinner`

This is a simple integer field component , based on standard HTML input control. It can be used when there is no need for advanced options of the `Spinner` component. You can set `min` and `max` values (default - no limits) and set the `step` value (default - 1).

```kotlin
Spinner(label = "Number 10 - 20", 
    min = 10, 
    max = 20, 
    step = 2)
```

### `i.k.f.upload.BootstrapUpload`

The kvision-bootstrap-upload module allows you to use a form component based on [Bootstrap fileinput](https://github.com/kartik-v/bootstrap-fileinput), which allows the user to select and upload files. It can be used as a standard file input element, with files being sent as a multi-part form submission or as an AJAX submission and it can be also used as a client-side file selection tool to be used with [FileReader API](https://developer.mozilla.org/en-US/docs/Web/API/FileReader) available in modern browsers. The `Upload` component has a lot of options to customize its appearance and functionality - see API documentation for more details.&#x20;

#### Standard HTML form submission

You can use the `Upload` component to upload files to the server with standard multi-part form action. You should create the `FormPanel` container with additional parameters (`FormMethod.POST`, `FormEnctype.MULTIPART`). In this mode you will be able to upload only one file at a time (unless you add more `Upload` controls). You should also add a button with type `SUBMIT`.

```kotlin
formPanel<Form>(FormMethod.POST, "/upload", FormEnctype.MULTIPART) {
    add(Form::text, Text(label = "Text", name = "text_field"))
    add(Form::upload, BootstrapUpload(label = "Upload file").apply {
        name = "file_field"
    })
    hPanel {
        button("Submit", type = ButtonType.SUBMIT)
    }
}
```

#### AJAX upload

To use this mode you have to create a server endpoint responsible for processing an AJAX request and returning the correctly formatted response (at least an empty JSON object {}). More information can be found on this [Bootstrap fileinput page](http://plugins.krajee.com/file-input#ajax-async). On the client side you can use all available options including multiple file uploads. There is no need to add any form parameters or submit buttons.

```kotlin
formPanel<Form> {
    add(Form::text, Text(label = "Text"))
    add(Form::upload, BootstrapUpload("/ajax-upload", label = "Upload file").apply {
        multiple = true
        explorerTheme = true
        dropZoneEnabled = false
        allowedFileTypes = setOf("image")
    })
}
```

In this mode you have to handle your uploaded files and the rest of your form data separately. Alternatively you can use `uploadExtraData` property and add your form data directly to your AJAX request.

```kotlin
formPanel<Form> {
    add(Form::text, Text(label = "Text"))
    add(Form::upload, BootstrapUpload("/ajax-upload", label = "Upload file").apply {
        uploadExtraData = { _, _ -> this@formPanel.getDataJson() }
    })
}
```

#### Client side file handling

Modern browsers support FileReader API and allow you to work with files entirely on the client side. The `FormPanel<K>` class has a suspending `getDataWithFileContent` extension function, which is  using Kotlin coroutines to read the content of all uploaded files as BASE64 dataURL strings. It makes it very easy to use such content in other KVision components (e.g. images or any HTML formatted text).

```kotlin
formPanel<Form> {
    add(Form::text, Text(label = "Caption"))
    add(Form::upload, BootstrapUpload("/", label = "Image").apply {
        showUpload = false
        showCancel = false
        explorerTheme = true
        allowedFileTypes = setOf("image")
    })
    hPanel {
        button("Use form data").onClickLaunch {
            val fdata = this@formPanel.getDataWithFileContent()
            val firstImage = fdata.upload?.firstOrNull()?.content
            if (firstImage != null) {
                Alert.show(fdata.text, "<img src=\"$firstImage\">", rich = true)
            }
        }
    }
}
```

{% hint style="info" %}
Note: You need to set `uploadUrl` parameter (e.g. to "/" value), even though it's not directly used in this mode.
{% endhint %}

### `i.k.f.number.Range`

This component can be used to allow a user to select numeric value with a slider. You need to configure `min` (default 0), `max` (default 100) and `step` (default 1) attributes.

```kotlin
Range(label = "Range field 10 - 20", min = 10, max = 20)
```

{% hint style="info" %}
Note: the actual appearance of the control may depend on your browser.
{% endhint %}
