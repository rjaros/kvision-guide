# Hot Module Replacement

Every KVision project utilize the HMR \(Hot Module Replacement\) feature of [Webpack](https://webpack.js.org/concepts/hot-module-replacement/). HMR can significantly speed up development by updating browser content automatically after changes are made in the source code. It allows you to retain the state of the application, too. Just override the `start` method with a `state` parameter.

{% tabs %}
{% tab title="App.kt" %}
```kotlin
package com.example

import pl.treksoft.kvision.Application
import pl.treksoft.kvision.panel.root
import pl.treksoft.kvision.require

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
```
{% endtab %}
{% endtabs %}

The HMR module calls `start` method after every change in the source code, and this method is responsible for recreating the user interface.

In case of a need to retain the state of the application, it should be returned as a `Map<String, Any>` from the`dispose` method. It will be sent back to the application in the `state` parameter of the `start` method.

