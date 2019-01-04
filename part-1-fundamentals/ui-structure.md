# UI structure

A KVision application is built with components and containers. A container can be a parent of one or more components \(called children\) and its purpose is to lay out children in some type of order. Every container is a component as well, so it can be a child of another container.

You can instantiate any KVision component and set any of its properties, but the component won't be rendered on the page until it is added to a container \(exception: [Modals](windows-and-modals.md)\). Of course this container has to be a child of another container and so on until the [Root container](root-container.md).

## Component

The main interface for a component is `pl.treksoft.kvision.core.Component`. It declares `parent` and `visible` properties and some universal methods for all components.

{% code-tabs %}
{% code-tabs-item title="Component.kt" %}
```kotlin
interface Component {
    var parent: Container?
    var visible: Boolean
    fun addCssClass(css: String): Component
    fun removeCssClass(css: String): Component
    fun getElement(): Node?
    fun getElementJQuery(): JQuery?
    fun getElementJQueryD(): dynamic
    ...
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

The base class for all components is [`pl.treksoft.kvision.core.Widget`](https://rjaros.github.io/kvision/api/pl.treksoft.kvision.core/-widget/index.html). All KVision visual components inherit \(directly or indirectly\) from the `Widget` class.

## Container

The main interface for a container is `pl.treksoft.kvision.core.Container`. It declares the methods to manage container's children.

{% code-tabs %}
{% code-tabs-item title="Container.kt" %}
```kotlin
interface Container : Component {
    fun add(child: Component): Container
    fun addAll(children: List<Component>): Container
    fun remove(child: Component): Container
    fun removeAll(): Container
    fun getChildren(): List<Component>
    ...
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

KVision offers a lot of different containers and they can implement other methods to add or remove children.

## DSL builders

Components can be added to containers explicitly, by calling appropriate methods \(`add`, `addAll`\), or by using DSL builders based on extension functions. Both ways allow you to get the reference of the created component for further use.

### Explicit calls

```kotlin
val panel = HPanel(spacing = 20, alignItems = FlexAlignItems.CENTER).apply {
    add(Label("A label."))
    val button = Button("Click me")
    add(button)
    // do something with a Button instance
}
```

### DSL

```kotlin
hPanel(spacing = 20, alignItems = FlexAlignItems.CENTER) {
    label("A label.")
    val button = button("Click me")
    // do something with a Button instance
}
```

As you can see from the above example KVision defines extension functions with names matching the component classes with a first character lower case \(e.g. `HPanel` -&gt; `hPanel`, `Label` -&gt; `label`, `Button` -&gt; `button`\). These functions takes the same parameters as the primary constructors, their receiver is `pl.treksoft.kvision.core.Container` and they are returning the reference to the created object instance.

At the moment the DSL builders allow you to use only basic `add` method of the `Container` interface. If you want to use any of the specialized add methods of different containers you must call them explicitly.

```kotlin
dockPanel {
    VPanel {
        width = 80.px
        height = 100.perc
    }.also {
        add(it, Side.LEFT) // Explicit call of the specialized add method of DockPanel
    }
    HPanel {
        height = 60.px
        width = 100.perc
    }.also {
        add(it, Side.DOWN) // Explicit call of the specialized add method of DockPanel
    }
    canvas(510, 320)
}
```

