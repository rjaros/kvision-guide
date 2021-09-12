# Icons and Images

## Icons

KVision has built-in support for free font icons from the [Font Awesome](https://fontawesome.com) project \(you have to include `kvision-fontawesome` module\) and from the [Bootstrap Icons](https://icons.getbootstrap.com/) project \(with `kvision-bootstrap-icons` module\). Components like buttons, links, drop-downs, select options and tabs have an `icon` property, which can be set to one of many available icon names. KVision supports all free Font Awesome style prefixes  - Solid \(`fas`\), Regular \(`far`\) and Brands \(`fab`\). You can check icon availability at [Font Awesome gallery page](https://fontawesome.com/icons?d=gallery&m=free).

```kotlin
val g = Button("A button with an icon", "fas fa-asterisk")
val fb = Link("A link with an icon", "https://google.com", "fab fa-google")
```

The dedicated `io.kvision.html.Icon` component lets you use those icons inside any container.

## Images

Components like buttons, links, tabs and the css background class have an `image` property, which can be used to display custom images. To use your own graphical resources, put them inside the `src/main/resources/img` directory and refer to them with a `require` function:

```kotlin
val i = Button("A button with an image") {
    image = require("img/dog.jpg")
}
```

{% hint style="info" %}
Note: All local files referenced in your application will be processed by Webpack and saved under a mangled name in the output directory.
{% endhint %}

To use an external image just use its URL address without `require`.

```kotlin
val l = Link("A link with an external image", image = "https://www.host.com/logo.png")
```

The dedicated `io.kvision.html.Image` component lets you use images inside any container. It gives you also some additional control over image align, shape and responsiveness:

```kotlin
val catImg = Image(require("img/cat.jpg"), alt = "A rounded and responsive cat",
    responsive = true, shape = ImageShape.ROUNDED)
val dogImg = Image(require("img/dog.jpg"), alt = "Centered dog in a circle",
    shape = ImageShape.CIRCLE, centered = true)
```

