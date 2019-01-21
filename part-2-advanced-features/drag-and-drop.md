# Drag and drop

All KVision components have built-in support for HTML5 drag and drop. Any component can be draggable and any other component can play a role of a drop target. To make a component draggable just call `setDragDropData` method, defined in the `Widget` class. You can set data format \(it can be "text/plain" or other mime-type\) and the data itself.

```kotlin
val element = Div("Element", align = Align.CENTER) {
    setDragDropData("text/plain", "element")
}
```

To create a drop target call `setDropTargetData` method. Use the data format parameter to allow dropping chosen draggables. You can access the data transferred from the source element in the callback function.

```kotlin
panel.setDropTargetData("text/plain") { data ->
    if (data == "element") {
        panel.add(element)
    }
}
```

