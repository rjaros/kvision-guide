# Forms

### Introduction

Forms are one of the most important parts of GUI of many applications, especially the ones which collect and manage data from the users. Traditionally handling HTML forms is a complex process, composed of many small steps like placing HTML form controls on the page, setting and displaying initial data, reading user data from individual HTML elements and finally transforming and validating the data. It gets even worse if you are using custom visual components for complex data types - date, time, rich text, multiple select, uploaded files etc.

KVision lets you work with forms in a very simple, consistent and efficient way. It hides all complex data transformations inside the framework logic and offers you a fully type-safe binding between your data and your forms. It has support for many different, both simple and complex, form controls. And it gives you ready to use data validation.

### Form data model

Form data is modeled with a standard Kotlin data class enhanced with `@Serializable` annotation from [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) library.  Every field of this model class holds the value of one input item rendered within a form. The model support most basic data types: `String`, `Number`(including `Int`and `Double`), `Boolean`, `Date` and  also a special type `List<KFile>` (a list of uploaded files).

```kotlin
@Serializable
data class Form(
    val text: String? = null,
    val password: String? = null,
    val password2: String? = null,
    val textarea: String? = null,
    val richtext: String? = null,
    @Contextual val date: Date? = null,
    @Contextual val time: Date? = null,
    val checkbox: Boolean = false,
    val radio: Boolean = false,
    val select: String? = null,
    val ajaxselect: String? = null,
    val spinner: Int? = null,
    val radiogroup: String? = null,
    val upload: List<KFile>? = null
)
```

{% hint style="info" %}
Note: You have to add `@Contextual` annotations to your `Date` fields in order to explicitly allow serialization with the KVision context. You can also use `@file:UseContextualSerialization(Date::class)` file annotation if you want to keep your model classes cleaner. You can find more information about this annotation in the [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/serializers.md#contextual-serialization) documentation.
{% endhint %}

### Form controls

Form controls are KVision components implementing one of six `FormControl` interfaces inside `io.kvision.form` package: `StringFormControl`, `NumberFormControl`, `BoolFormControl`, `TriStateFormControl`, `DateFormControl` and `KFilesFormControl`. KVision comes with a bunch of build-in or modular form components.

| Component                      | Interface             | Module                             | Description                                                                              |
| ------------------------------ | --------------------- | ---------------------------------- | ---------------------------------------------------------------------------------------- |
| `i.k.f.check.CheckBox`         | `BoolFormControl`     | built-in                           | A check-box.                                                                             |
| `i.k.f.check.Radio`            | `BoolFormControl`     | built-in                           | A radio-button.                                                                          |
| `i.k.f.check.RadioGroup`       | `StringFormControl`   | built-in                           | A group of radio-buttons.                                                                |
| `i.k.f.check.TriStateCheckBox` | `TriStateFormControl` | built-in                           | A tri-state check-box.                                                                   |
| `i.k.f.text.Text`              | `StringFormControl`   | built-in                           | A text field.                                                                            |
| `i.k.f.text.Password`          | `StringFormControl`   | built-in                           | A text field for password input.                                                         |
| `i.k.f.text.TextArea`          | `StringFormControl`   | built-in                           | A text area.                                                                             |
| `i.k.f.select.SimpleSelect`    | `StringFormControl`   | built-in                           | A standard select component.                                                             |
| `i.k.f.range.Range`            | `NumberFormControl`   | built-in                           | A range selection field.                                                                 |
| `i.k.f.time.DateTime`          | `DateFormControl`     | kvision-bootstrap-datetime         | A date and/or time selection control.                                                    |
| `i.k.f.text.RichText`          | `StringFormControl`   | kvision-richtext                   | A rich text editor.                                                                      |
| `i.k.f.select.Select`          | `StringFormControl`   | kvision-bootstrap-select           | An advanced select box with support for multiple selection and AJAX data source support. |
| `i.k.f.select.SelectRemote`    | `StringFormControl`   | kvision-bootstrap-select-remote    | A select box for fullstack interfaces.                                                   |
| `i.k.f.spinner.Spinner`        | `NumberFormControl`   | kvision-bootstrap-spinner          | A spinner control for number selection.                                                  |
| `i.k.f.upload.Upload`          | `KFilesFormControl`   | kvision-bootstrap-upload           | An upload file control with preview and multi-selection.                                 |
| `i.k.f.text.Typeahead`         | `StringFormControl`   | kvision-bootstrap-typeahead        | A typeahed (autocomplete) text field with support for data source.                       |
| i.k.f.text.TypeaheadRemote     | StringFormControl     | kvision-bootstrap-typeahead-remote | A typeahead (autocomplete) text field for fullstack interfaces.                          |

{% hint style="info" %}
Note: `RadioGroup` and `Select` controls always return `String` values. Multiple selections are comma-separated.&#x20;

There is also `GenericRadioGroup<T>` component, which can return value of any type, but it can't be used inside `Form`/`FormPanel` containers.&#x20;
{% endhint %}

### Form containers

KVision provides a dedicated container for working with forms - `FormPanel<K>`. To create a `FormPanel<K>` instance you need to specify a serializer for your model data class.

```kotlin
val formPanel = FormPanel(serializer = Form.serializer())
```

&#x20;You can also use a DSL builder extension function, which automatically uses default serializer for your data class.

```kotlin
val formPanel = formPanel<Form> {}
```

Under the hood `FormPanel` container uses non-visual form container - `Form<K>`, which can be used by developers to implement their own form containers.

### Data binding

You add form controls to the `FormPanel` using `add` method of the container, and at the same time you bind your controls to your data model by referencing class properties. The binding is type-safe, e.g. you can't bind `StringFormControl` to `Boolean` or `Date` field.

```kotlin
formPanel<Form> {
    add(
        Form::text,
        Text(label = "Text field") {
            placeholder = "Enter text"
        })
    add(Form::password, Password(label = "Password field"))
    add(Form::textarea, TextArea(label = "Text area field"))
    add(Form::richtext, RichText(label = "Rich text field"))
    add(Form::date, DateTime(format = "YYYY-MM-DD", label = "Date field"))
    add(Form::time, DateTime(format = "HH:mm", label = "Time field"))
    add(Form::checkbox, CheckBox(label = "Required checkbox"))
    add(Form::radio, Radio(label = "Radio button"))
    add(Form::select, Select(options = listOf("first" to "First option", "second" to "Second option"),
            label = "Simple select"
        )
    )
    add(Form::spinner, Spinner(label = "Spinner field 10 - 20", min = 10, max = 20))
    add(Form::radiogroup, RadioGroup(listOf("option1" to "First option", "option2" to "Second option"),
            inline = true, label = "Radio button group"
        )
    )
    add(Form::upload, Upload("/", multiple = true, label = "Upload files (images only)") {
        explorerTheme = true
        dropZoneEnabled = false
        allowedFileTypes = setOf("image")
    })
}
```

#### Manual binding

If you need to manage your form directly you can create your layout using standard KVision DSL builders and bind your form controls manually using `bind` extension method.

```kotlin
formPanel<Form> {
	hPanel(spacing = 5) {
		text(label = "text 1").bind(Form::text, required = true)
		vPanel(spacing = 5) {
			textArea(label = "text 2").bind(Form::text2)
			textArea(label = "text 3").bind(Form::text3)
		}
	}
}
```

After binding your fields you can treat a `FormPanel<K>` instance as a kind of a "black box" - you manage all your data flow with just a few methods - mostly `setData()` and `getData()`.

```kotlin
val formPanel = formPanel<Form> {
    // fields binding
}
val formData = Form(text = "Text data", password = "pass", 
    spinner = 15, select = "second")
formPanel.setData(formData)
val unmodifiedFormData = formPanel.getData()
println(unmodifiedFormData == formData) // true
```

### Form parameters

`FormPanel<K>` container can layout form elements with three standard Bootstrap layouts: normal, horizontal and inline. The class constructor allows to specify other HTML form parameters as well.

```kotlin
val formPanel = formPanel<Form>(
    type = FormType.HORIZONTAL,
    method = FormMethod.GET,
    action = "form",
    enctype = FormEnctype.MULTIPART
) {
    // ...
}
```

### Validation

KVision forms support validation for single fields and for the form as a whole. You can easily mark some fields as required, specify all needed validation functions and specify error messages, which will be displayed by the browser after validation action. Validation functions give you easy access to the values entered in the form.&#x20;

```kotlin
formPanel<Form> {
    add(
        Form::password,
        Text(label = "Password"),
        required = true,
        requiredMessage = "This field is required",
        validatorMessage = { "Enter more than 8 characters" }
    ) { (it.getValue()?.length ?: 0) >= 8 }
    add(
        Form::password2,
        Text(label = "Confirm password"),
        required = true,
        validatorMessage = { "Enter more than 8 characters" }
    ) { (it.getValue()?.length ?: 0) >= 8 }
    validator = {
        it[Form::password] == it[Form::password2]
    }
    validatorMessage = { "The passwords are not the same." }
}
val validationResult = formPanel.validate()
```

{% hint style="info" %}
Note: `validatorMessage` parameters are functions with the same parameters as `validator` functions.&#x20;
{% endhint %}

{% hint style="info" %}
Note: `requiredMessage` and `validatorMessage` parameters are optional. The default values for them are respectively "Value is required" and "Invalid value". You should use these parameters if you want to give more precise descriptions of the problems or when you want to internationalize your application.
{% endhint %}

### Fieldsets

You can group several subsequent form fields within a single fieldset container by adding the same `legend` parameter to the following `add` method calls.

```kotlin
add(
    Form::date,
    DateTime(format = "YYYY-MM-DD", label = tr("Date field with a placeholder")).apply {
        placeholder = tr("Enter date")
    }, legend = tr("Date and time fieldset")
)
add(
    Form::time,
    DateTime(format = "HH:mm", label = tr("Time field")), 
    legend = tr("Date and time fieldset")
)
```

### Custom field types

Form data model doesn't support custom types out of the box, but it's possible to add support for your own type as long as you can use such type with a form control based on `StringFormControl` interface.

Define a custom class for your model. It needs a `toString()` method to convert its data to the `String` value used within a form.

```kotlin
class ObjectId(val id: Int) {
    override fun toString(): String {
        return "$id"
    }
}
```

Next you need to define a custom serializer for this class.

```kotlin
object ObjectIdSerializer : KSerializer<ObjectId> {
    override val descriptor: SerialDescriptor = buildClassSerialDescriptor("com.example.ObjectId")
    override fun deserialize(decoder: Decoder): ObjectId {
        val str = decoder.decodeString()
        return ObjectId(str.toInt())
    }    
    override fun serialize(encoder: Encoder, obj: ObjectId) {
        encoder.encodeString(obj.id.toString())
    }
}
```

Now you can use this class within your model, by adding `@Contextual` annotation.

```kotlin
@Serializable
data class Form(
    val text: String? = null,
    val password: String? = null,
    @Contextual val objectId: ObjectId? = null
)
```

When creating a `FormPanel` container, you need to pass a reference to your serializer.

```kotlin
formPanel<Form>(customSerializers = mapOf(ObjectId::class to ObjectIdSerializer)) {
    // ...
}
```

Finally you can add your data binding with a special `addCustom` method.

```kotlin
addCustom(Form::objectId, Text(label = "Object Id"))
```

### Dynamic forms

Sometimes it's not possible to know the data model of the form because the fields need to be added and/or removed dynamically. For such use cases KVision allows you to model your data with a `Map<String, Any?>` type. To work with dynamic forms you need to create a `FormPanel` container with a simplified `form()` DSL builder function. Such call will return a `FormPanel<Map<String, Any?>>` instance. When adding or binding form controls use string identifiers instead of property references.

```kotlin
val form: FormPanel<Map<String, Any?>> = form(type = FormType.HORIZONTAL) {
    text(label = "First text field").bind("text1")
    text(label = "Second text field").bind("text2", required = true)
    checkBox(label = "CheckBox").bind("check1")
    simpleSelect(listOfPairs("First option", "Second option"), 
      label = "Select value", emptyOption = true).bind("select1", required = true)
}
```

Although all the fields are defined dynamically, you can still treat the form as a whole. You use `getData()` and `setData()` functions using standard Kotlin maps. The identifiers used for bindings will be the keys in the map.

```kotlin
val map = form.getData()
val textValue1 = map["text1"].unsafeCast<String?>()
val textValue2 = map["text2"].unsafeCast<String?>()

form.setData(mapOf("check1" to true, "select1" to "First option"))
```

{% hint style="info" %}
When using dynamic forms you are intentionally loosing type safety. You need to make sure the values stored in the map are matching the types of form controls.&#x20;
{% endhint %}
