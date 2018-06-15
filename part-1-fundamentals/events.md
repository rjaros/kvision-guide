# Events

KVision allows you to listen to all the standard DOM events and also many custom events from many different components. You bind to an event with a `setEventListener` method of the base `Widget` class.

```kotlin
widget.setEventListener {
    mousedown = { e ->
        console.log(e)
    }
}
```

Some components define a shortcut method `onClick` for the click event, so you can write:

```kotlin
button.onClick { e ->
    console.log(e)
}
```

Event function parameter is not required if not used.

```kotlin
button.setEventListener {
    mousedown = {
        console.log("Mouse down")
    }
}
```

You can bind many different event handlers with one call to `setEventListener` method.

```kotlin
button.setEventListener {
    mousedown = {
        console.log("Mouse down")
    }
    mousemove = {
        console.log("Mouse move")
    }
    mouseup = {
        console.log("Mouse up")
    }
}
```

Unfortunately, due to internal Snabbdom implementation, you can't bind two or more handlers to the same event - the later handler will overwrite the prior one.

## Self reference

In the code of a basic form of an event handler you can get the reference to the component instance with a special `self` variable.

```kotlin
Div("Click to hide ...").setEventListener { 
    click = {
        self.hide() // self is of type Widget
    }
}
```

The type of `self` variable is `Widget` in the above example. If you want to call a dedicated method of a receiver component you can call `setEventListener` with a correct type parameter.

```kotlin
Div("Click to change").setEventListener<Div> { 
    click = {
        self.content = "Changed" // self is of type Div
    }
}
```



