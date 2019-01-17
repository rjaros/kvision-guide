# Form controls guide

All KVision form controls are available in two variants. The first variant, with class name ending with "Input" \(e.g. `TextInput`\), is a basic, fully-functional component ready to be used as a standalone widget. The second variant, with class name without "Input" \(e.g. `Text`\), is a wrapper component implementing one of `FormControl` interfaces and is intended primarily for use with a `FormPanel` container. This variant can display a label, a validation information and has some additional CSS styling consistent with Bootstrap forms components. From the developer point of view both variants have the same properties and methods \(a side of `label` and `rich` properties available for second variant only\). 

{% hint style="info" %}
The following reference do not differentiate both variants of form controls. 
{% endhint %}

All KVision form controls have the following properties:

* `value` - allows to get or set the value of the component
* `name` - defines a `name` attribute of the HTML input element
* `size` - allows to set the size of the form control \(normal, large or small Bootstrap style\)
* `disabled` - defines whether the form control is disabled

## Text fields

### \`\`[`p.t.k.f.text.Text`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.text/-text/index.html)\`\`

This component is rendered as a standard HTML text field and is used to get simple, textual data from the user. The `type` property can be set to any of these values: `TEXT`, `PASSWORD`, `EMAIL`, `TEL`, `COLOR`, `SEARCH`, `URL` and is bound to the `type` attribute of the HTML input field. Modern browsers can render such fields with additional elements \(e.g. color chooser\). Other properties allow you to define a placeholder, a maximum length, autofocus or autocomplete attributes and define if the control should be read-only.  

```kotlin
Text(type = TextInputType.URL, label = "WWW").apply {
    placeholder = "Enter the address"
    maxlength = 255
    autofocus = true
    autocomplete = false
    readonly = false
}
```

### \`\`[`p.t.k.f.text.Password`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.text/-password/index.html)\`\`

This component is a direct subclass of `p.t.k.form.text.Text` with `type` property set to `TextInputType.PASSWORD`.

```kotlin
Password(label = "Password")
```

### \`\`[`p.t.k.f.text.TextArea`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.text/-text-area/index.html)\`\`

This component is rendered as a standard HTML textarea field and is used to get longer, multi-line textual data from the user. It can be sized with `cols` and `rows` properties \(mapped to standard HTML attributes\).

```kotlin
TextArea(cols = 100, rows = 5, label = "Enter some text")
```

 Alternative way \(and preferred nowadays\) is to set CSS `width` and `height` properties.

```kotlin
TextArea(width = 300.px, height = 120.px, label = "Enter some text")
```

Other properties allow you to define a placeholder, a maximum length, autofocus or hard wrap attributes and define if the control should be read-only.

```kotlin
TextArea(width = 300.px, 
        height = 120.px, 
        label = "Enter some text", 
        placeholder = "Enter some text",
        autofocus = true,
        wrapHard = true,
        readonly = true)
```

### \`\`[`p.t.k.f.text.RichText`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.text/-rich-text/index.html)\`\`

The kvision-richtext module allows you to use a dedicated form control based on a modern [Trix Editor](https://trix-editor.org/) component. It can be used to get rich text from the user. This component renders a text editing field with a toolbar containing basic formatting options \(bold, italic, strikethrough, heading, quote, code, link and lists with indentations\). The `value` property contains properly formatted HTML markup. Other properties allow you to define a placeholder and autofocus attribute.

```kotlin
RichText(value = "<b>Bold text</b>", 
    label = "Rich text field with a placeholder",
    placeholder = "Enter some text",
    autofocus = true)
```

## Checkboxes and radiobuttons

### \`\`[`p.t.k.f.check.CheckBox`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.check/-check-box/index.html)\`\`

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

### \`\`[`p.t.k.f.check.Radio`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.check/-radio/index.html)\`\`

This component is similar to the `CheckBox` component, but it is rendered as a radiobutton. By using the `name` property, you can group some radiobuttons together, with only one control selected at a time.  

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

### \`\`[`p.t.k.f.check.RadioGroup`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.check/-radio-group/index.html)\`\`

Although it is possible to use basic `Radio` components to build and manage radiobutton groups \(you would have to check which radio in a group is selected\), KVision offers a dedicated `RadioGroup` component, which allows you to treat a group of radiobuttons as a single component returning a String value. It can be initialized with a list of options \(key to value pairs\) and the selected option can be easily set or get with the `value` property.

```kotlin
val radioGroup = RadioGroup(
    listOf("option1" to "First option", "option2" to "Second option"),
    inline = true, label = "Radio button group"
)
radioGroup.value = "option1"
println(radioGroup.value)
```

## Select boxes

### \`\`[`p.t.k.f.select.Select`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.select/-select/index.html)\`\`

The kvision-select module allows you to use a sophisticated form control based on a [Bootstrap Select](https://github.com/silviomoreto/bootstrap-select). It's a full-featured component, configurable with plenty of options. It can be used for a simple select picker with a few static options as well as a searchable, dynamic lists pulled over the network with an AJAX extension. The `Select` component can be initialized with a list of options \(key to values pairs\).

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option"),
    label = "Simple select"
)
```

It can also be initialized by adding options components \(`SelectOption` and `SelectOptGroup` classes\) by hand. This way you can also use some more advanced features, like option groups, subtexts, icons and dividers.

```kotlin
Select(label = "Simple select with features").apply {
    selectOption("first", "First Option", "Subtext")
    selectOption(divider = true)
    selectOption("second", "Second Option")
    selectOptGroup("Option group") {
        selectOption("g1", "Group 1", icon = "fa-apple")
        selectOption("g2", "Group 2", icon = "fa-google")
    }
}
```

You can select many options at the same time  with `multiple` property. You can use `maxOptions` property to limit the number of selected options.

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option", "third" to "Third option"),
    multiple = true,
    label = "Multiple select"
).apply {
    maxOptions = 2
}
```

If your select has a long list of options you can use `liveSearch` property to add a simple search box.

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option", "third" to "Third option"),
    label = "Select with a search box"
).apply {
    liveSearch = true
}
```

By default the `Select` component is resized to 100% of its parent width, but this can be changed with properties`selectWidth` \(any CSS size\) or `selectWidthType` \(`AUTO` or `FIT` values\).

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option"),
    label = "Resized select"
).apply {
    // selectWidth = 800.px
    selectWidthType = SelectWidthType.AUTO
}
```

Other properties allow to define a placeholder, a button style, an autofocus attribute and to automatically generate an empty option \(to be able to de-select value\).

```kotlin
Select(
    options = listOf("first" to "First option", "second" to "Second option"),
    label = "Styled Select"
).apply {
    placeholder = "Select a value"
    style = ButtonStyle.DANGER
    autofocus = true
    emptyOption = true
}
```

`Select` component can also work with a remote data source, by integrating [Ajax Bootstrap Select](https://github.com/truckingsim/Ajax-Bootstrap-Select) extension. To use AJAX mode you should initialize`ajaxOptions` property with an instance of   [`pl.treksoft.kvision.form.select.AjaxOptions`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.select/-ajax-options/index.html) data class. See API documentation for more information about options and parameters of AJAX mode.

```kotlin
Select(label = "Select with remote data source").apply {
    ajaxOptions = AjaxOptions("https://api.github.com/search/repositories", preprocessData = {
        it.items.map { item ->
            obj {
                this.value = item.id
                this.text = item.name
            }
        }
    }, data = obj {
        q = "{{{q}}}"
    }, minLength = 3, requestDelay = 1000)
}
```

### \`\`[`p.t.k.f.select.RemoteSelect`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.form.select/-remote-select/index.html)\`\`

This component is contained in the kvision-select-remote module and is a special version of `Select` control, tailored for use with KVision's multiplatform services. It allows you to define remote data source for a select picker control with a callable reference to a special function \(all data transformations will be done automatically by the framework\). You can find more information about server side interface in [part 3](../part-3-server-side-interface.md) of this guide.

```kotlin
// server side
class MainService: IMainService {
    overwrite fun getDictionary(search: String?, initial: String?): List<RemoteSelectOption> {
        // ...
    }
}
...
// client side
RemoteSelect(serviceManager = MainServiceManager, 
    function = MainService::getDictionary,
    label = "Select option from dictionary"
)
```

## Others

### `p.t.k.f.time.DateTime`

### `p.t.k.f.spinner.Spinner`

### `p.t.k.f.upload.Upload`



