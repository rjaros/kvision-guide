# UI Structure

A KVision application is built with components and containers. A container can be a parent of one or more components \(called children\) and its purpose is to lay out children in some type of order. Every container is a component as well, so it can be a child of another container.

You can instantiate any KVision component and set any of its properties, but the component won't be rendered on the page until it is added to a container \(exception: [Modals](../3.-optional-ui-functionality-via-modules/bootstrap/windows-and-modals.md)\). Of course this container has to be a child of another container and so on until the [Root container](root-container.md).

## Component

The main interface for a component is `io.core.Component`. It declares `parent` and `visible` properties and some universal methods for all components.

{% code title="Component.kt" %}
```kotlin
interface Component {
    var parent: Container?
    var visible: Boolean
    fun addCssClass(css: String): Component
    fun removeCssClass(css: String): Component
    fun getElement(): Node?
    ...
}
```
{% endcode %}

The base class for all components is `io.kvision.core.Widget`. All KVision visual components inherit \(directly or indirectly\) from the `Widget` class.

## Container

The main interface for a container is `io.kvision.core.Container`. It declares the methods to manage container's children.

{% code title="Container.kt" %}
```kotlin
interface Container : Component {
    fun add(child: Component): Container
    fun add(position: Int, child: Component): Container
    fun addAll(children: List<Component>): Container
    fun remove(child: Component): Container
    fun removeAt(position: Int): Container
    fun removeAll(): Container
    fun disposeAll(): Container
    fun getChildren(): List<Component>
    ...
}
```
{% endcode %}

KVision offers a lot of different containers and they can implement other methods to add or remove children.

## DSL builders

Components can be added to containers explicitly, by calling appropriate methods \(`add`, `addAll`\), or by using DSL builders based on extension functions. Both ways allow you to get the reference of the created component for further use.

### Explicit calls

```kotlin
val panel = HPanel(spacing = 20, alignItems = AlignItems.CENTER).apply {
    add(Span("A label."))
    val button = Button("Click me")
    add(button)
    // do something with a Button instance
}
```

### DSL

```kotlin
hPanel(spacing = 20, alignItems = AlignItems.CENTER) {
    span("A label.")
    val button = button("Click me")
    // do something with a Button instance
}
```

As you can see from the above example KVision defines extension functions with names matching the component classes with a first character lower case \(e.g. `HPanel` -&gt; `hPanel`, `Span` -&gt; `span`, `Button` -&gt; `button`\). These functions takes the same parameters as the primary constructors, their receiver is `io.kvision.core.Container` and they are returning the reference to the created object instance.

The DSL builders allow you to use only basic `add` method of the `Container` interface. If you want to use any of the specialized add methods of different containers you have to use dedicated DSL methods, provided by some of the containers:

```kotlin
dockPanel {
    left {
        vPanel {
            width = 80.px
            height = 100.perc
        }
    }
    down {
        hPanel {
            height = 60.px
            width = 100.perc
        }
    }
    center {
        canvas(510, 320)
    }
}
```

You can also always just call the appropriate methods explicitly.

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

