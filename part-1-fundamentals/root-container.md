# Root container

Every KVision application must have at least one Root container \( an instance of `pl.treksoft.kvision.panel.Root` class\). Root container is initialized by an extension function of the Application class, with an ID attribute of HTML tag, which must be present in the main `index.html` file.

{% code-tabs %}
{% code-tabs-item title="App.kt" %}
```kotlin
import pl.treksoft.kvision.panel.root

override fun start() {
    root("kvapp") {
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="index.html" %}
```markup
<!DOCTYPE html>
<html>
...
<body>
<div id="kvapp"></div>
</body>
</html>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The Root container is the root of the components tree, which is inserted into the DOM in the place of the selected HTML element. This tree is managed, rendered and refreshed by the Root container.

It is possible to have more than one Root container in one application. In such case there will be more than one independent components trees.

