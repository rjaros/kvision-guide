# Handlebars.js Templates

Support for [Handlebars.js](https://handlebarsjs.com/) templates is contained in kvision-handlebars module and is based on webpack's [handlebars loader](https://github.com/pcardune/handlebars-loader). Templates are automatically transformed to JavaScript functions during the build process of the application.

You put your template files (with `*.hbs` extension) into `src/jsMain/resources/modules/hbs` directory of your application. Then in your code you can use `@JsModule` and reference your `*.hbs` files. All components which render textual content (`io.kvision.html.Tag` class and subclasses) have `template` and `templates` properties. The second one allows you to define different templates for other supported languages (see. [Internationalization](../2.-frontend-development-guide/internationalization.md)).

```kotlin
@JsModule("/kotlin/modules/hbs/template.hbs")
external val templateHbs: dynamic

@JsModule("/kotlin/modules/hbs/template.en.hbs")
external val templateEnHbs: dynamic

@JsModule("/kotlin/modules/hbs/template.pl.hbs")
external val templatePlHbs: dynamic

div {
    template = templateHbs
}

div {
    templates = mapOf(
        "en" to templateEnHbs,
        "pl" to templatePlHbs
    )
}
```

To actually call the template function and render its output in your app, you need to set some data. You can use `templateData` property and use plain JavaScript object. To generate one from your data  you can use `obj` function builder.

```kotlin
import io.kvision.utils.obj

div {
    template = templateHbs
    templateData = obj {
        name = "Alan"
        hometown = "Somewhere, TX"
        kids = arrayOf(obj {
            name = "Jimmy"
            age = "12"
        }, obj {
            name = "Sally"
            age = "5"
        })
    }
}
```

You can also use `toObj` extension function if you store your data in a class (or classes) with `@Serializable` annotation.

```kotlin
import kotlinx.serialization.Serializable
import io.kvision.utils.JSON.toObj

@Serializable
data class Kid(val name: String, val age: Int)
@Serializable
data class Person(val name: String, val hometown: String, val kids: List<Kid>)

val person = Person("Alan", "Somwhere, TX", listOf(Kid("Jimmy", 12), Kid("Sally", 5)))

div {
    template = templateHbs
    templateData = person.toObj()
}

```

The `setData` extension function is a convenient shortcut for the above.

```kotlin
div {
    template = templateHbs
    setData(person)
}
```
