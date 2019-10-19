# Theming

By default KVision includes the standard [Bootstrap](https://getbootstrap.com/docs/4.3/getting-started/introduction/) look and feel. It's modern, elegant and proven in many production environments. But there are other possibilities as well. Just include `kvision-bootstrap` and `kvision-bootstrap-css` modules in your `build.gradle.kts` file.

## Adding a custom CSS file to your application

You can add a custom CSS file to define your own CSS classes, which can be used throughout your code \(almost every component supports the `classes: Set<String>` constructor parameter and the `addCssClass(css: String)` and `removeCssClass(css: String)` methods\). Of course you can also overwrite and change the standard Bootstrap classes.

You can add as many CSS files as you wish. Just save your `*.css` files in `src/main/resources/css` directory and `require` them in your main `App` object.

{% code-tabs %}
{% code-tabs-item title="App.kt" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

## Replacing Bootstrap CSS with a custom one

You can remove `kvision-bootstrap-css` module from your application and include different CSS file in the main `index.html` of your application. It can be loaded from any local or remote source.

{% code-tabs %}
{% code-tabs-item title="index.html" %}
```markup
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>KVision App</title>
    <link rel="stylesheet" href="mybootstrap.min.css" >
    <script type="text/javascript" src="main.bundle.js"></script>
</head>
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can create such a file manually, [build your own Bootstrap](https://getbootstrap.com/docs/4.3/getting-started/build-tools/) version from sources or use a third party [customizer](http://bootstrapcustomizer.com/). To include a local version of Bootstrap, simply place `mybootstrap.min.css` in the `src/main/web/css` directory.

## Using free themes from [Bootswatch](https://bootswatch.com/)

There are some great free themes ready to use available at [Bootswatch CDN](https://www.bootstrapcdn.com/bootswatch/). For instance to use Materia \(material-like\) theme make the following change to your `index.html`.

{% code-tabs %}
{% code-tabs-item title="index.html" %}
```markup
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>KVision Address Book</title>
    <link href="https://stackpath.bootstrapcdn.com/bootswatch/4.3.1/materia/bootstrap.min.css" rel="stylesheet" integrity="sha384-SYbiks6VdZNAKT8DNoXQZwXAiuUo5/quw6nMKtFlGO/4WwxW86BSTMtgdzzB9JJl" crossorigin="anonymous">
    <script type="text/javascript" src="main.bundle.js"></script>
</head>
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Using KVision without Bootstrap

KVision can also be used with no Bootstrap at all. Just exclude all \*bootstrap\* modules from your `build.gradle.kts` file. You won't be able to use components from these modules, which depend on Bootstrap's JavaScript and styling. But you can still create a fully functional application \(see [TodoMVC example](https://github.com/rjaros/kvision-examples#todomvc)\).

