# Forms

### Introduction

Forms are one of the most important parts of GUI of many applications, especially the ones which collect and manage data from the users. Traditionally handling of HTML forms is a complex process, composed of many small steps like placing HTML form controls on the page, setting and displaying initial data, reading user data from individual HTML elements and finally transforming and validating the data. It gets even worst if using external, composite form controls for complex data types - date, time, rich text, multiple select, uploaded files etc.

KVision lets you work with forms in a very simple, consistent and efficient way. It hides all complex data transformations inside the framework logic and gives you fully type-safe binding between your data and your forms. It has support for many different, both simple and complex, form controls. And it gives you ready to use data validation.

### Form data model

Form data is modeled with a standard Kotlin data class enhanced with `@Serializable` annotation from [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) library.  Every field of this model class holds the value of one form control rendered within a form. Form controls support most basic data types: `String`, `Number`\(including `Int`and `Double`\), `Boolean`, `Date` and  also a special type `List<KFile>` \(a list of uploaded files\).

```kotlin
@Serializable
data class Form(
    val text: String? = null,
    val password: String? = null,
    val password2: String? = null,
    val textarea: String? = null,
    val richtext: String? = null,
    val date: Date? = null,
    val time: Date? = null,
    val checkbox: Boolean = false,
    val radio: Boolean = false,
    val select: String? = null,
    val ajaxselect: String? = null,
    val spinner: Int? = null,
    val radiogroup: String? = null,
    val upload: List<KFile>? = null
)
```

### Form controls

Form controls are KVision components implementing one of five `FormControl` interfaces inside `pl.treksoft.kvision.form` package: `StringFormControl`, `NumberFormControl`, `BoolFormControl`, `DateFormControl` and `KFilesFormControl`. KVision comes with a bunch of build-in or modular form components, which can be used with forms.

| Component | Interface | Module | Description |
| :--- | :--- | :--- | :--- |
| `p.t.k.f.check.CheckBox` | `BoolFormControl` | built-in | A check-box. |
| `p.t.k.f.check.Radio` | `BoolFormControl` | built-in | A radio-button. |
| `p.t.k.f.check.RadioGroup` | `StringFormControl` | built-in | A group of radio-buttons \(with only one of them selected at a time\). |
| `p.t.k.f.text.Text` | `StringFormControl` | built-in | A text field. |
| `p.t.k.f.text.Password` | `StringFormControl` | built-in | A text field for a masked password input. |
| `p.t.k.f.text.TextArea` | `StringFormControl` | built-in | A text area. |
| `p.t.k.f.time.DateTime` | `DateFormControl` | kvision-datetime | A date and/or time selection control. |
| `p.t.k.f.text.RichText` | `StringFormControl` | kvision-richtext | A rich text editor. |
| `p.t.k.f.select.Select` | `StringFormControl` | kvision-select | A select box with support for multiple selection and AJAX data source support. |
| `p.t.k.f.select.RemoteSelect` | `StringFormControl` | kvision-select-remote | A select box for multi-platform server-side connectivity. |
| `p.t.k.f.spinner.Spinner` | `NumberFormControl` | kvision-spinner | A spinner control for easy number selection. |
| `p.t.k.f.upload.Upload` | `KFilesFormControl` | kvision-upload | An upload file control with preview and multi-selection.  |

{% hint style="info" %}
Note: `RadioGroup` and `Select` controls always return `String` values. Multiple selections are comma-separated.
{% endhint %}

### Form containers

KVision provides a dedicated container for working with forms - `FormPanel<K>`. It can layout form elements with three standard Bootstrap layouts: normal, horizontal and inline. Under the hood it uses non-visual form container - `Form<K>`, which can be used by developers to implement their own form containers.

You add form controls to the `FormPanel` and at the same time you bind them to your data model. The binding is type-safe, e.g. you can't bind `StringFormControl` with `Boolean` or `Date` field.

```kotlin
formPanel<Form> {
    add(
        Form::text,
        Text(label = "Text field").apply {
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
    add(Form::upload, Upload("/", multiple = true, label = "Upload files (images only)").apply {
        explorerTheme = true
        dropZoneEnabled = false
        allowedFileTypes = setOf("image")
    })
}
```

After binding your fields you can treat a `FormPanel<K>` instance as a kind of a "black box" - you manage all your data flow with just a few methods - `setData()`, `getData()` and `clearData()`.



