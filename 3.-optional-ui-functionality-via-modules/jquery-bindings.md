# jQuery Bindings

The `kvision-jquery` module allows you to work with the [jQuery](https://jquery.com/) library.

## jQuery object

You can use the `io.kvision.jquery.jQuery` object to create a jQuery instance with all supported selector types. You can use them to directly access and modify underlying DOM elements and their attributes.

```kotlin
import io.kvision.jquery.invoke
import io.kvision.jquery.jQuery

jQuery("#ident").addClass("blue").show()
```

## getElementJQuery() method

The extension function `getElementJQuery()` can be used to access the jQuery instance bound to the underlying DOM element of the component. It gives you the possibility to easily access and modify the default behavior of KVision components.

{% hint style="info" %}
This method should only be called after the component has been rendered. It returns `null` otherwise. It's a good practice to overwrite `afterInsert` method from the `Widget` class in your own classes, and call `getElementJQuery()` from there. Alternatively you can use `addAfterInsertHook` method of the `Widget` class to wait until rendering is complete without the need to create your own class.
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
