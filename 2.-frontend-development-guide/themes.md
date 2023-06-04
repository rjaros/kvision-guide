# Theming

By default KVision includes the standard [Bootstrap](https://getbootstrap.com/) look and feel. It's modern, elegant and proven in many production environments. Just include `kvision-bootstrap` module in your `build.gradle.kts` file. But there are other possibilities as well.&#x20;

## Adding a custom CSS file to your application

You can add a custom CSS file to define your own CSS classes, which can be used throughout your code (almost every component supports the `className: String?` constructor parameter and the `addCssClass(css: String)` and `removeCssClass(css: String)` methods). Of course you can also overwrite and change the standard Bootstrap classes.

You can add as many CSS files as you wish. Just save your `*.css` files in `src/main/resources/css` directory and `require` them in your main `App` object.

{% code title="App.kt" %}
```kotlin
class App : Application() {
    init {
        require("css/kvapp.css")
        require("css/other.css")
    }
    
    override fun start() {
    // ...
    }
}
```
{% endcode %}

## Replacing Bootstrap CSS with a custom one

You can remove `BootstrapCssModule` initializer from your application and include different CSS file in the main `index.html` of your application. It can be loaded from any local or remote source.

{% code title="index.html" %}
```markup
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>KVision App</title>
    <link rel="stylesheet" href="css/mybootstrap.min.css" >
    <script type="text/javascript" src="main.bundle.js"></script>
</head>
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode %}

You can create such a file manually or [build your own Bootstrap](https://getbootstrap.com/docs/5.1/customize/overview/) version from sources. To include a local version of Bootstrap, simply place `mybootstrap.min.css` in the `src/main/web/css` directory.

## Using free themes from [Bootswatch](https://bootswatch.com/)

There are some great free themes ready to use available at [JSDelivr](https://www.jsdelivr.com/package/npm/bootswatch). For instance to use Materia (material-like) theme make the following change to your `index.html`.

{% code title="index.html" %}
```markup
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>KVision Address Book</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootswatch@5.1.0/dist/materia/bootstrap.min.css" integrity="sha256-T8h7oW5tHp4MhFhmRoDI8xjnB2ld89qSgc3s9g8T0uU=" crossorigin="anonymous">
    <script type="text/javascript" src="main.bundle.js"></script>
</head>
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode %}

## Using KVision without Bootstrap

KVision can also be used with no Bootstrap at all. Just exclude all \*bootstrap\* modules from your `build.gradle.kts` file. You won't be able to use components from these modules, which depend on Bootstrap's JavaScript and styling. But you can use any other CSS framework (see examples for [Fomantic UI](https://github.com/rjaros/kvision-examples/tree/master/fomantic) and [Patternfly](https://github.com/rjaros/kvision-examples/tree/master/patternfly)).&#x20;

## Dark mode

KVision fully supports Bootstrap dark mode. You can use `io.kvision.theme.ThemeManager` component from the `kvision-bootstrap` module to work with color themes.&#x20;

```kotlin
import io.kvision.theme.ThemeManager

class MyApp : Application() {
    init {
        ThemeManager.init()
    }
}
```

By default theme manager is initialized with auto-detected mode and saves the mode selected by the user in the brower local storage. You can modify this behavior with additional parameters of the `init()` method.

```kotlin
import io.kvision.theme.Theme
import io.kvision.theme.ThemeManager

class MyApp : Application() {
    init {
        ThemeManager.init(initialTheme = Theme.DARK, remember = false)
    }
}
```

You can use `ThemeManager.theme` property to manually select the color theme.

```kotlin
button("Switch to dark mode").onClick {
    ThemeManager.theme = Theme.DARK
}
```

You can also use built-in `ThemeSwitcher`, component which is a `Button` subclass designed as the color mode toggler (you need to also include Font Awesome module for icons).

```kotlin
themeSwitcher(style = ButtonStyle.OUTLINESECONDARY, round = true)
```
