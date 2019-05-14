# Layout containers

Layout containers allow you to position your components in a different ways to create both simple and complex views. Different container types can be stacked together to achieve even the most sophisticated layouts. Core containers are available in `pl.treksoft.kvision.panel` package.

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

This container creates a popular tabbed layout, with tabs on the top, left or right side of the content. You use the dedicated `addTab` method of the `TabPanel` class to create tabs with given names, icons, images and routing URLs.

```kotlin
tabPanel {
    val tab1Content = Div("Apple description")
    val tab2Content = Div("Google description")
    val tab3Content = Div("Microsoft description")
    addTab("Apple", tab1Content, "fa-apple")
    addTab("Google", tab2Content, "fa-google")
    addTab("Microsoft", tab3Content, "fa-windows")
}
```

For tabs displayed on the left or right side of the content, you need to declare the ratio between the size of the tabs and the content. The `sideTabSize` parameter of `TabPanel` constructor takes one of six enumerated values \(from `SIZE_1` to `SIZE_6`\) which mean 1/11, 2/10, 3/9, 4/8, 5/7, and 6/6 ratios. The default ratio is 3/9.

```kotlin
tabPanel(tabPosition = TabPosition.LEFT, sideTabSize = SideTabSize.SIZE_2) {
    val tab1Content = Div("Apple description")
    val tab2Content = Div("Google description")
    val tab3Content = Div("Microsoft description")
    addTab("Apple", tab1Content, "fa-apple")
    addTab("Google", tab2Content, "fa-google")
    addTab("Microsoft", tab3Content, "fa-windows")
}
```

## DockPanel

This container can have up to five children, and it shows them in five distinct positions - CENTER, UP, DOWN, LEFT and RIGHT. The default `add` method of the `DockPanel` class \(used also by the DSL builders\) puts the given component in the CENTER position. You have to use the dedicated `add(child: Component, position: Side)` method to put your child components in the other four positions.

```kotlin
dockPanel {
    div("CENTER") {
        margin = auto
    }
    add(Div("LEFT"), Side.LEFT)
    add(Div("RIGHT"), Side.RIGHT)
    add(Div("UP"), Side.UP)
    add(Div("DOWN"), Side.DOWN)
}
```

## FlexPanel

The `FlexPanel` class allows you to display children components with all the power of[ CSS Flexible Box Layout Module](https://www.w3.org/TR/css-flexbox/) W3C recommendation. In the flex layout model, the children components can be laid out horizontally or vertically, left to right, right to left, top to bottom or bottom to top. Children can change their sizes, either growing or shrinking. The specification is quite complex and most of the available CSS attributes are supported with Kotlin enum values used with the `FlexPanel` class. You use the dedicated `add(child: Component, order: Int? = null, grow: Int? = null, shrink: Int? = null, basis: Int? = null, alignSelf: FlexAlignItems? = null, classes: Set = setOf())` method of `FlexPanel` class to add children components with additional flexbox attributes:

```kotlin
flexPanel(
    FlexDir.ROW, FlexWrap.WRAP, FlexJustify.FLEXSTART, FlexAlignItems.CENTER,
    spacing = 5
) {
    add(Div("1"), order = 3)
    add(Div("2"), order = 1)
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

The `GridPanel` class allows you to display children components with the use of [CSS Grid Layout Module](https://www.w3.org/TR/css-grid/) W3C recommendation - a two-dimensional layout system, which allows the children to be positioned into arbitrary slots in a predefined flexible or fixed-size grid. This specification is even more complex than Flexbox, but is still supported mostly with Kotlin enum values and type-safe parameters. You use the dedicated `add(child: Component, columnStart: Int? = null, rowStart: Int? = null, columnEnd: String? = null, rowEnd: String? = null, area: String? = null, justifySelf: GridJustify? = null, alignSelf: GridAlign? = null, classes: Set = setOf())` method of `GridPanel` class to add children components with additional grid attributes:

```kotlin
gridPanel(columnGap = 5, rowGap = 5, justifyItems = GridJustify.CENTER) {
    add(Div("1,1"), 1, 1)
    add(Div("1,2"), 1, 2)
    add(Div("2,1"), 2, 1)
    add(Div("2,2"), 2, 2)
}
```

## ResponsiveGridPanel

This container allows you to position children components inside Bootstrap's popular responsive 12-columns grid system. It's well suited for mobile websites and applications, but can also be used as an alternative to the CSS Grid. The `ResponsiveGridPanel` class allows you to choose grid size \(with XS, SM, MD, LG enum values\) and automatically wraps children components into appropriate rows and cols elements. The number of rows and columns can be declared as parameters of the class constructor or calculated on the fly based on children added to the container. You use the dedicated `add(child: Component, col: Int, row: Int, size: Int = 0, offset: Int = 0)` method of the `ResponsiveGridPanel` class to add children to selected positions in the grid:

```kotlin
responsiveGridPanel {
    add(Div("1,1"), 1, 1)
    add(Div("3,1"), 3, 1)
    add(Div("2,2"), 2, 2)
    add(Div("3,3"), 3, 3)
}
```

