# Layout containers

Layout containers allow you to position your components in a different ways to create both simple and complex views. Different container types can be stacked together to achieve even the most sophisticated layouts. Core containers are available in `io.kvision.panel` package.

## SimplePanel

This container puts its children components in a simple sequence, without any additional formatting or markup. It's the most basic container and is the base class for all other containers. It is internally rendered as a `DIV` HTML element.

```kotlin
simplePanel {
    span("First span")
    span("Second span")
    span("Third span")
}
```

It will be rendered as:

```markup
<div>
    <span>First span</span>
    <span>Second span</span>
    <span>Third span</span>
</div>
```

## StackPanel

This container displays only one of its children components at a time. You can use properties and methods of the `StackPanel` class to manage its children visibility.

```kotlin
val panel = stackPanel(activateLast = true) {
    span("First span")
    span("Second span")
}
```

It will be initially rendered as:

```markup
<div>
    <span>Second span</span>
</div>
```

You can change active child component by setting `activeIndex` or `activeChild` properties. After this:

```text
panel.activeIndex = 0
```

the content in the browser will change to:

```markup
<div>
    <span>First span</span>
</div>
```

## SplitPanel

This container divides the available space into two areas and provides a possibility to resize the panes by the user.

```kotlin
splitPanel {
    div("Left area")
    div("Right area")
}
splitPanel(Direction.HORIZONTAL) {
    div("Top area")
    div("Bottom area")
}
```

{% hint style="warning" %}
`SplitPanel` container is required to have exactly two children. If there are more or less than two children nothing will be rendered at all.
{% endhint %}

## TabPanel

{% hint style="info" %}
This layout container is only available with the `kvision-bootstrap` module.
{% endhint %}

This container creates a popular tabbed layout, with tabs on the top, left or right side of the content.

```kotlin
tabPanel {
    tab("Apple", "fab fa-apple") {
        div("Apple description")
    }
    tab("Google", "fab fa-google") {
        div("Google description")
    }
    tab("Microsoft", "fab fa-windows") {
        div("Microsoft description")
    }
}
```

For tabs displayed on the left or right side of the content, you need to declare the ratio between the size of the tabs and the content. The `sideTabSize` parameter of `TabPanel` constructor takes one of six enumerated values \(from `SIZE_1` to `SIZE_6`\) which mean 1/11, 2/10, 3/9, 4/8, 5/7, and 6/6 ratios. The default ratio is 3/9.

```kotlin
tabPanel(tabPosition = TabPosition.LEFT, sideTabSize = SideTabSize.SIZE_2) {
    tab("Apple", "fab fa-apple") {
        div("Apple description")
    }
    tab("Google", "fab fa-google") {
        div("Google description")
    }
    tab("Microsoft", "fab fa-windows") {
        div("Microsoft description")
    }
}
```

Tabs can be made closable. A closable tab displays a cross icon on the tab bar. When the user clicks this icon, the `removeTab` method is called and the tab is deleted from the container. Two events are sent - `tabClosing` and `tabClosed` with the tab index saved in `detail.data` property. The first event is cancelable - you can call `preventDefault()` method to cancel the action.

```kotlin
tabPanel {
    tab("Apple", "fab fa-apple", closable = true) {
        div("Apple description")
    }
    tab("Google", "fab fa-google", closable = true) {
        div("Google description")
    }
    tab("Microsoft", "fab fa-windows", closable = true) {
        div("Microsoft description")
    }
    onEvent {
        tabClosing = { e ->
            if (e.detail.data == 2) {
                e.preventDefault()
            } else {
                console.log("Closing tab on index ${e.detail.data}")
            }
        }
        tabClosed = { e ->
            console.log("Closed tab on index ${e.detail.data}")
        }
    }
}
```

`TabPanel` container implementation allows to:

* dynamically add and remove tabs
* get the list of tabs and dynamically modify tab properties \(label, icon, image, closable\)
* find tab containing a given child component
* dynamically reorder tabs
* allow user to reorder tabs with drag & drop

## DockPanel

This container can have up to five children, and it shows them in five distinct positions - CENTER, UP, DOWN, LEFT and RIGHT. The default `add` method of the `DockPanel` class \(used also by the DSL builders\) puts the given component in the CENTER position. Use the dedicated DSL builder functions to put your child components in the other four positions.

```kotlin
dockPanel {
    div("CENTER") {
        margin = auto
    }
    left {
        div("LEFT")
    }
    right {
        div("RIGHT")
    }
    up {
        div("UP")
    }
    down {
        div("DOWN")
    }
}
```

## FlexPanel

The `FlexPanel` class allows you to display children components with all the power of[ CSS Flexible Box Layout Module](https://www.w3.org/TR/css-flexbox/) W3C recommendation. In the flex layout model, the children components can be laid out horizontally or vertically, left to right, right to left, top to bottom or bottom to top. Children can change their sizes, either growing or shrinking. The specification is quite complex and most of the available CSS attributes are supported with Kotlin enum values used with the `FlexPanel` class. You use the dedicated `add(child: Component, order: Int? = null, grow: Int? = null, shrink: Int? = null, basis: Int? = null, alignSelf: FlexAlignItems? = null, classes: Set = setOf())` method of `FlexPanel` class or `options` DSL builder function to add children components with additional flexbox attributes:

```kotlin
flexPanel(
    FlexDirection.ROW, FlexWrap.WRAP, JustifyContent.FLEXSTART, AlignItems.CENTER,
    spacing = 5
) {
    options(order = 3) {
        div("1")
    }
    options(order = 1) {
        div("2")
    }
    add(Div("3"), order = 2)
}
```

## HPanel, VPanel

Both`HPanel` and `VPanel` classes are direct subclasses of `FlexPanel` and are convenient shortcuts for frequently used horizontal \(left to right\) and respectively vertical \(top to bottom\) flexbox layouts:

```kotlin
hPanel(spacing = 5) {
    div("1")
    div("2")
    div("3")
}
vPanel(spacing = 5) {
    div("1")
    div("2")
    div("3")
}
```

## GridPanel

The `GridPanel` class allows you to display children components with the use of [CSS Grid Layout Module](https://www.w3.org/TR/css-grid/) W3C recommendation - a two-dimensional layout system, which allows the children to be positioned into arbitrary slots in a predefined flexible or fixed-size grid. This specification is even more complex than Flexbox, but is still supported mostly with Kotlin enum values and type-safe parameters. You use the dedicated `add(child: Component, columnStart: Int? = null, rowStart: Int? = null, columnEnd: String? = null, rowEnd: String? = null, area: String? = null, justifySelf: JustifyItems? = null, alignSelf: AlignIems? = null, classes: Set = setOf())` method of `GridPanel` class or `options` DSL builder function to add children components with additional grid attributes:

```kotlin
gridPanel(columnGap = 5, rowGap = 5, justifyItems = JustifyItems.CENTER) {
    options(1, 1) {
        div("1,1")
    }
    options(1, 2) {
        div("1,2")
    }
    add(Div("2,1"), 2, 1)
    add(Div("2,2"), 2, 2)
}
```

## ResponsiveGridPanel

{% hint style="info" %}
This layout container is only available with the `kvision-bootstrap` module.
{% endhint %}

This container allows you to position children components inside Bootstrap's popular responsive 12-columns grid system. It's well suited for mobile websites and applications, but can also be used as an alternative to the CSS Grid. The `ResponsiveGridPanel` class allows you to choose grid size \(with SM, MD, LG, XL enum values\) and automatically wraps children components into appropriate rows and cols elements. The number of rows and columns can be declared as parameters of the class constructor or calculated on the fly based on children added to the container. You use the dedicated `add(child: Component, col: Int, row: Int, size: Int = 0, offset: Int = 0)` method of the `ResponsiveGridPanel` class or `options` DSL builder function to add children to selected positions in the grid:

```kotlin
responsiveGridPanel {
    options(1, 1) {
        div("1,1")
    }
    options(3, 1) {
        div("3,1")
    }
    add(Div("2,2"), 2, 2)
    add(Div("3,3"), 3, 3)
}
```

## Non-DSL containers

All standard KVision containers are annotated with `@DslMarker`, which provides [strict scope control](https://kotlinlang.org/docs/type-safe-builders.html#scope-control-dslmarker) over implicit receivers. However, since KVision 4.1.1 there are some additional containers available, which are not marked with DSL annotations. They have names starting with `Basic*` prefix \(`BasicPanel`, `BasicTabPanel`, `BasicFlexPanel`, etc.\) and are marked as experimental with `@ExperimentalNonDslContainer`annotation. These containers can be used as base classes for your own components, when strict receiver scope control is not desirable.

```kotlin
@ExperimentalNonDslContainer
class MyComponent: BasicPanel() {
    val button = Button("B1").apply {
        onClick { doSomething() }
    }
    lateinit var button2: Button
    init {
        div {
            buttonGroup {
                add(button)
                button2 = button("B2") {
                    onClick { doSomething() }
                }
            }
        }
    }
    fun doSomething() {}
}
```

{% hint style="info" %}
To compile code with non-DSL container classes you need to use `@ExperimentalNonDslContainer` annotation. Sometimes in maybe easier to use `@OptIn(ExperimentalNonDslContainer::class)` or even apply this annotation automatically with Gradle:

```kotlin
sourceSets.all {
    languageSettings.apply {
        useExperimentalAnnotation("io.kvision.core.ExperimentalNonDslContainer")
    }
}
```
{% endhint %}

