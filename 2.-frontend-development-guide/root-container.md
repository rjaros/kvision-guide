# Root Container

Every KVision application must have at least one Root container \( an instance of `io.kvision.panel.Root` class\). Root container is initialized by an extension function of the Application class, with an ID attribute of HTML tag, which must be present in the main `index.html` file.

{% code title="App.kt" %}
```kotlin
import io.kvision.panel.root

override fun start() {
    root("kvapp") {
    }
}
```
{% endcode %}

{% code title="index.html" %}
```markup
<!DOCTYPE html>
<html>
...
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode %}

The Root container is the root of the components tree, which is inserted into the DOM in the place of the selected HTML element. This tree is managed, rendered and refreshed by the Root container.

It is possible to have more than one Root container in one application. In such case there will be more than one independent components trees.

