# jQuery bindings

[jQuery](https://jquery.com/) is used internally by the framework and is available for use in all KVision applications with a wrapper library [jquery-kotlin](https://github.com/rjaros/jquery-kotlin).

## jQuery object

You can use the `pl.treksoft.jquery.jQuery` object to create a jQuery instance with all supported selector types. You can use them to directly access and modify underlying DOM elements and their attributes.

```kotlin
import pl.treksoft.jquery.jQuery

jQuery("#ident").addClass("blue").show()
```

## getElementJQuery\(\) method

Every KVision component has a method `getElementJQuery()`, which can be used to access the jQuery instance bound to the underlying DOM element of the component. It gives you the possibility to easily access and modify the default behavior of KVision components.

{% hint style="info" %}
This method should only be called after the component has been rendered. It returns `null` otherwise. It's a good practice to overwrite `afterInsert` method from the `Widget` class in your own classes, and call `getElementJQuery()` from there.
{% endhint %}

```kotlin
override fun afterInsert(node: VNode) {
    this.getElementJQuery()?.parent("#id")?.click { e ->
        console.log("clicked parent: ${e.clientX}, ${e.clientY}")
    }
}
```

There is also a `getElementJQueryD()` method which returns a value of `dynamic` type. It can be used to initialize and run code from external NodeJS modules and events to create custom KVision components.

```kotlin
override fun afterInsert(node: VNode) {
    getElementJQueryD()?.selectpicker("render").ajaxSelectPicker()
}
```

