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

The `onEvent` extension function returns `Int` value, which can be used to remove the event listener from the component.

```kotlin
val id = button.onEvent {
    mousemove = {
        console.log("Mouse move")
    }
}
button.removeEventListener(id)
```

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

## Event Flows

The `kvision-event-flow` module, which depends on Kotlin coroutines library, allows you to easily create Flow streams of events. Using flows, you can process events from KVision components with the power of Kotlin flow API \(e.g. handling backpressure or combining multiple flows\). The module gives you universal `eventFlow` extension function and the dedicated `clickFlow`, `inputFlow` and `changeFlow` functions for the corresponding event types.

```kotlin
val button = button("A button")
button.clickFlow.onEach {
    console.log("Button clicked")
}.launchIn(GlobalScope)

button.eventFlow("mousemove").conflate().onEach {
    console.log("Mouse X: "+(it.second as MouseEvent).clientX)
    console.log("Mouse Y: "+(it.second as MouseEvent).clientY)
    delay(1000)
}.launchIn(GlobalScope)
```

