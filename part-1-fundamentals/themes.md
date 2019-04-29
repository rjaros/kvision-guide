# Theming

By default KVision includes the standard [Bootstrap](http://getbootstrap.com/docs/3.3/) \(version 3\) look and feel. It's modern, elegant and proven in many production environments. But there are other possibilities as well.

## Adding a custom CSS file to your application

You can add a custom CSS file to define your own CSS classes, which can be used throughout your code \(almost every component supports the `classes: Set<String>` constructor parameter and the `addCssClass(css: String)` and `removeCssClass(css: String)` methods\). Of course you can also overwrite and change the standard Bootstrap classes.

You can add as many CSS files as you wish. Just save your `*.css` files in `src/main/resources/css` directory and `require` them in your main `App` object.

{% code-tabs %}
{% code-tabs-item title="App.kt" %}
```kotlin
object App : ApplicationBase {
    override fun start(state: Map<String, Any>) {
    // ...
    }
    override fun dispose(): Map<String, Any> {
    // ...
    }

    val css1 = require("./css/kvapp.css")
    val css2 = require("./css/other.css")
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Replacing Bootstrap CSS with a custom one

When you include a file called `bootstrap.min.css` \(from any local or remote location\) in the main `index.html` of your application, KVision will automatically disable standard Bootstrap CSS. This way you  can replace the original stylesheet with anything you wish.

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
    <link rel="stylesheet" href="bootstrap.min.css" >
    <script type="text/javascript" src="main.bundle.js"></script>
</head>
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can create such a file manually or use [Customize Bootstrap](http://getbootstrap.com/docs/3.3/customize/) online tool offered by Bootstrap's website. To include a local version of bootstrap, simply place `bootstrap.min.css` in the `src/main/web/css` directory.

## Using free themes from [Bootswatch](https://bootswatch.com/3/)

There are some great free themes ready to use available at [Bootswatch CDN](https://www.bootstrapcdn.com/legacy/bootswatch/). For instance to use Paper \(material-like\) theme make the following change to your `index.html`.

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
    <link href="https://maxcdn.bootstrapcdn.com/bootswatch/3.3.7/paper/bootstrap.min.css" rel="stylesheet" integrity="sha384-awusxf8AUojygHf2+joICySzB780jVvQaVCAt1clU3QsyAitLGul28Qxb2r1e5g+" crossorigin="anonymous">
    <script type="text/javascript" src="main.bundle.js"></script>
</head>
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
Note: Use [Bootswatch](https://bootswatch.com/3/) themes for Bootstrap version 3.
{% endhint %}

## Using KVision without Bootstrap

KVision can also be used with no Bootstrap at all. But you will not be able to use all parts of the framework is such configuration. Some components will not work correctly or will not work at all \(especially Modals, Dropdowns, Windows, Date picker, Spinner, TabPanel and other components dependent on Bootstrap's JavaScript and it also disables support for Font Awesome icons\). But you can still create a fully functional application \(see [TodoMVC example](https://github.com/rjaros/kvision-examples#todomvc)\).   

To disable Bootstrap completely just remove the `kvision-bootstrap` dependency from the `build.gradle` file.  As a result no Bootstrap CSS and no Bootstrap JavaScript will be included in the resulting application.
