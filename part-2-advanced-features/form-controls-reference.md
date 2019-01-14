# Form controls reference

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

## Checkboxes and radio-buttons

### `p.t.k.f.check.CheckBox`

### `p.t.k.f.check.Radio`

### `p.t.k.f.check.RadioGroup`

## Select boxes

### `p.t.k.f.select.Select`

### `p.t.k.f.select.RemoteSelect`

## Others

### `p.t.k.f.time.DateTime`

### `p.t.k.f.spinner.Spinner`

### `p.t.k.f.upload.Upload`



