# Events

KVision allows you to listen to all the standard DOM events and also many custom events from many different components. You bind your action to an event with the `onEvent` extension function.

```kotlin
import pl.treksoft.kvision.core.onEvent

widget.onEvent {
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
button.onEvent {
    mousedown = {
        console.log("Mouse down")
    }
}
```

You can bind many different event handlers with one call to the `onEvent` function.

```kotlin
button.onEvent {
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

Unfortunately, due to internal Snabbdom implementation, you can't bind two or more handlers to the same event - the second handler will always overwrite the first one.

## Self reference inside an event handler

In the code of an event handler you can get the reference to the component instance with a special `self` variable.

```kotlin
Div("Click to hide ...").onEvent {
    click = {
        self.hide()
    }
}

Div("Click to change").onEvent {
    click = {
        self.content = "Changed"
    }
}
```

