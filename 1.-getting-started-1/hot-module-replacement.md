# Hot Module Replacement

Every KVision project utilizes the HMR \(Hot Module Replacement\) feature of [Webpack](https://webpack.js.org/concepts/hot-module-replacement/). HMR can significantly speed up development by updating browser content automatically after changes are made in the source code. It allows you to retain the state of the application, too. Just override the `start` method with a `state` parameter.

{% code title="App.kt" %}
```kotlin
package com.example

import io.kvision.Application
import io.kvision.module
import io.kvision.panel.root

class App : Application() {

    override fun start(state: Map<String, Any>) {
        root("kvapp") {
            // TODO
        }
    }

    override fun dispose(): Map<String, Any> {
        return mapOf()
    }

}

fun main() {
    startApplication(::App, module.hot)
}
```
{% endcode %}

The HMR module calls the `start` method after every change in the source code, and this method is responsible for recreating the user interface.

In case of a need to retain the state of the application, it should be returned as a `Map<String, Any>` from the`dispose` method. It will be sent back to the application in the `state` parameter of the `start` method.

