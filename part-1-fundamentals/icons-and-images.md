# Icons and images

## Icons

KVision has built-in support for font icons distributed with [Bootstrap version 3](https://getbootstrap.com/docs/3.3/components/#glyphicons) \(glyphicons\) and [Font Awesome version 4](https://fontawesome.com/v4.7.0/icons/).  Components like buttons, links, drop-downs, select options and tabs have an `icon` property, which can be set to one of many available icon names. To use a glyphicon from Bootstrap just use the name of the selected icon \(e.g. "asterisk"\). To use Font Awesome icon use the name with "fa-" prefix \(e.g. "fa-asterisk"\).  Some examples:

```kotlin
val g = Button("A button with a glyphicon", "asterisk")
val fb = Link("A link with a Font Awesome icon", "https://google.com", "fa-asterisk")
```

The dedicated `pl.treksoft.kvision.html.Icon` component lets you use those icons inside any container.

## Images

Components like buttons, links, tabs and the css background class have an `image` property, which can be used to display custom images. To use your own graphical resources, put them inside the `src/main/resources/img` directory and refer to them with a `require` function:

```kotlin
val i = Button("A button with an image") {
    image = require("./img/dog.jpg")
}
```

{% hint style="info" %}
Note: All local files referenced in your application will be processed by Webpack and saved under a mangled name in the output directory.
{% endhint %}

To use an external image just use its URL address without `require`.

```kotlin
val l = Link("A link with an external image", image = "https://www.host.com/logo.png")
```

The dedicated `pl.treksoft.kvision.html.Image` component lets you use images inside any container. It gives you also some additional control over image align, shape and responsiveness:

```kotlin
val catImg = Image(require("./img/cat.jpg"), alt = "A rounded and responsive cat",
    responsive = true, shape = ImageShape.ROUNDED)
val dogImg = Image(require("./img/dog.jpg"), alt = "Centered dog in a circle",
    shape = ImageShape.CIRCLE, centered = true)
```
