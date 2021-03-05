# jQuery and DOM bindings

[jQuery](https://jquery.com/) is used internally by the framework and is available for use in all KVision applications with a wrapper library [jquery-kotlin](https://github.com/rjaros/jquery-kotlin).

## jQuery object

You can use the `io.kvision.jquery.jQuery` object to create a jQuery instance with all supported selector types. You can use them to directly access and modify underlying DOM elements and their attributes.

```kotlin
import io.kvision.jquery.invoke
import io.kvision.jquery.jQuery

jQuery("#ident").addClass("blue").show()
```

## getElementJQuery\(\) method

Every KVision component has a method `getElementJQuery()`, which can be used to access the jQuery instance bound to the underlying DOM element of the component. It gives you the possibility to easily access and modify the default behavior of KVision components.

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

## DOM bindings

Every KVision component is bound to the DOM tree after it has been rendered. You can get access to the underlying  `HTMLElement` object with the `getElement()` method from the `Widget` class. 

