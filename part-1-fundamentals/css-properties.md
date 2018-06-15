# CSS Properties

Every KVision component has a full range of properties based on CSS specification. Most of them are 100% type-safe - based on enumeration values, dedicated classes and functions. You can of course use custom CSS stylesheets and assign predefined classes to your components  \(as explained in the [Theming](themes.md#adding-a-custom-css-file-to-your-application) chapter\), but KVision gives you a choice. With CSS properties you can style any component size, position, margins, paddings, borders, colors, backgrounds, text and fonts with pure Kotlin code. 

Most of the CSS properties are defined in [pl.treksoft.kvision.core.StyledComponent](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.core/-styled-component/index.html) class.

An example of setting CSS properties for a DIV element.

```kotlin
div("A label with custom CSS styling") {
    margin = 10.px
    padding = 20.px
    fontFamily = "Times New Roman"
    fontSize = 32.px
    fontStyle = FontStyle.OBLIQUE
    fontWeight = FontWeight.BOLDER
    fontVariant = FontVariant.SMALLCAPS
    textDecoration = TextDecoration(TextDecorationLine.UNDERLINE, TextDecorationStyle.DOTTED, Col.RED)
}
```

