# Basic Components

## HTML markup

KVision contains classes for creating typical HTML markup of a web page. The main class for this purpose is `io.kvision.html.Tag`, which allows you to render any HTML element. This class is also a container, so instances of `Tag` can be nested inside other `Tag` objects. There are a few subclasses of `Tag` - `Div` , `P`, `Span`, `H1`, `H2`, `H3`, `H4`, `H5`, `H6`, `Main`, `Section`, `Header`, `Footer` - which render the most used HTML elements. With DSL builders you can declare simple markup as follows:

```kotlin
div {
    span("A simple text")
    span("A text with custom CSS styling") {
        fontFamily = "Times New Roman"
        fontSize = 32.px
        textDecoration = TextDecoration(TextDecorationLine.UNDERLINE, TextDecorationStyle.DOTTED, Color.name(Col.RED))
    }
    tag(TAG.CODE, "Some text written in <code></code> HTML tag.")
}
```

And it will be rendered as:

```markup
<div>
<span>A simple text</span>
<span style="font-family: Times New Roman; font-size: 32px; text-decroration: underline dotted red">
A test with custom CSS styling
</span>
<code>Some text written in &lt;code&gt;&lt;/code&gt; HTML tag.</code>
</div>
```

### Lists

You can use the `io.kvision.html.ListTag` class to create HTML lists. You can use a `List<String>` value to quickly populate the list:

```kotlin
div {
    listTag(ListType.UL, listOf("First list element", "Second list element", "Third list element"))
}
```

but you can also treat `ListTag` as a typical container:

```kotlin
div {
    listTag(ListType.OL) {
        tag(TAG.H4, "Header")
        button("Button")
        image(require("./img/cat.png"))
    }
}
```

### Tables

You can use classes from the `io.kvision.table.*` package to create HTML tables:

```kotlin
table(
    listOf("Column 1", "Column 2", "Column 3"),
    setOf(TableType.BORDERED, TableType.SMALL, TableType.STRIPED, TableType.HOVER),
    responsiveType = ResponsiveType.RESPONSIVE
) {
    row {
        cell("Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce nec fringilla turpis.")
        cell("Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce nec fringilla turpis.")
        cell("Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce nec fringilla turpis.")
    }
    row {
        cell("Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce nec fringilla turpis.")
        cell("Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce nec fringilla turpis.")
        cell("Lorem ipsum dolor sit amet, consectetur adipiscing elit. Fusce nec fringilla turpis.")
    }
}
```

For interactive, editable tables KVision also supports [`Tabulator`](../3.-optional-ui-functionality-via-modules/tabulator-tables.md)``

### Links

To create a link use `io.kvision.html.Link`class:

```
div {
    link("A link to Google", "http://www.google.com")
}
```

## Dynamic content

Note that all the generated HTML markup is fully dynamic and is bound to the state of the KVision components. If you change content or styling properties of any visible object, it will automatically re-render the content shown in the browser. These changes can be triggered by any source (timers, coroutines, network events) but probably most often they will be triggered by user interaction.

```kotlin
link("A link to Google", "http://www.google.com").onEvent {
    mouseover = {
        self.label = "A link to Microsoft"
        self.url = "http://www.microsoft.com"
    }
}
```

## Rich text

All components which render textual content allow you to declare such content as "rich text". If you set the `rich` property to `true`, the content will be treated as raw HTML. The framework takes care of making the output markup valid HTML if it's not. This code:

```kotlin
p(
    "Rich <b>text</b> <i>written</i> with <span style=\"font-family: Verdana; font-size: 14pt\">" +
            "any <strong>forma</strong>tting</span>.",
    rich = true
)
```

will render:

```markup
<p><span style="display: contents;">Rich <b>text</b> <i>written</i> with
<span style="font-family: Verdana; font-size: 14pt;">any <strong>forma</strong>tting
</span>.</span></p>
```

{% hint style="info" %}
Notice the extra `<span style="display: contents;">` element surrounding the given content.
{% endhint %}

## XSS Protection

By default KVision is safe from XSS - all text data is processed by the virtual DOM library and is fully sanitized before rendering in the browser. There are two exceptions to watch out for:

* Intentional use of rich text (described above), which is unsafe by design.
* Use of [Handlebars.js templates](../3.-optional-ui-functionality-via-modules/handlebars.js-templates.md), which are also rendered directly (using rich text internally).

If you need to use rich text with untrusted data (e.g. some input from a user), you should sanitize all the data before rendering with some proven sanitizer library.&#x20;

## Custom attributes

All KVision components support additional, custom attributes using `setAttribute`, `getAttribute` and `removeAttribute` methods from the `Widget` class.

```kotlin
button(text = "X", className = "close") {
    setAttribute("data-dismiss", "alert")
}
```
