# Hot Module Replacement

The template project contains files necessary to utilize the HMR \(Hot Module Replacement\) feature of [Webpack](https://webpack.js.org/concepts/hot-module-replacement/). HMR can significantly speed up development by updating browser content automatically after changes are made in the source code. It allows you to retain the state of the application, too.

{% code-tabs %}
{% code-tabs-item title="Main.kt" %}
```kotlin
package com.example

import pl.treksoft.kvision.hmr.ApplicationBase
import pl.treksoft.kvision.hmr.module
import kotlin.browser.document

fun main(args: Array<String>) {
    var application: ApplicationBase? = null

    val state: dynamic = module.hot?.let { hot ->
        hot.accept()

        hot.dispose { data ->
            data.appState = application?.dispose()
            application = null
        }

        hot.data
    }

    if (document.body != null) {
        application = start(state)
    } else {
        application = null
        document.addEventListener("DOMContentLoaded", { application = start(state) })
    }
}

fun start(state: dynamic): ApplicationBase? {
    if (document.getElementById("kvapp") == null) return null
    @Suppress("UnsafeCastFromDynamic")
    App.start(state?.appState ?: emptyMap())
    return App
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The main KVision application class is defined as an object extending `ApplicationBase` class.

{% code-tabs %}
{% code-tabs-item title="App.kt" %}
```kotlin
package com.example

import pl.treksoft.kvision.hmr.ApplicationBase
import pl.treksoft.kvision.panel.Root
import pl.treksoft.kvision.require

object App : ApplicationBase {

    private lateinit var root: Root

    override fun start(state: Map<String, Any>) {
        root = Root("kvapp") {
            // TODO
        }
    }

    override fun dispose(): Map<String, Any> {
        root.dispose()
        return mapOf()
    }

}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The HMR module calls `start` method after every change in the source code, and this method is responsible for recreating the user interface.

In case of a need to retain the state of the application, it should be returned as a `Map<String, Any>` from the`dispose` method. It will be sent back to the application in the `state` parameter of the `start` method.

