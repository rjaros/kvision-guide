# Events

KVision allows you to listen to all the standard DOM events and also many custom events from many different components. You bind your action to an event with the `onEvent` extension function.

```kotlin
import io.kvision.core.onEvent

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

If you are using coroutines you can also use `onClickLaunch` method:

```kotlin
button.onClickLaunch { e ->
    someSuspendingFunction()
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

### Sending events to components

There are several ways to send events to components.

One is to call functions on [HTMLElement](w3c-snabdom-and-kvision-elements.md), i.e.

```kotlin
// note that this won't work unless the button is mounted; see the link above
// for ways around this
button.getElement()?.click()
```

If there isn't a function representing your event, you can instead use [dispatchEvent](https://kotlinlang.org/api/latest/jvm/stdlib/org.w3c.dom.events/-event-target/dispatch-event.html)(), i.e.

```kotlin
button.getElement()?.dispatchEvent(MouseEvent("click"))
```

### KVision custom events

`SplitPanel`, `Window`, `Tabulator`, `TabPanel` and some OnsenUI components dispatch different custom events. You can use the name of the event just like with standard events.&#x20;

```kotlin
tabulator.onEvent {
    rowClickTabulator = {
        console.log("Row selected.")
    }
}

window.onEvent {
    resizeWindow = {
        console.log("Window resized.")
    }
}
```

### &#x20;Bootstrap custom events

For events defined by Bootstrap components, which cannot be used like above because of their names, you need to use `event()` extension function.

```kotlin
modal.onEvent {
    event("shown.bs.modal") {
        console.log("Bootstrap modal is shown.")
    }
    event("hidden.bs.modal") {
        console.log("Bootstrap modal is hidden")
    }
}
```

### jQuery events

For events defined by jQuery based components (`Upload`) use `jqueryEvent()` extension function from the `kvision-jquery` module.

```kotlin
bootstrapUpload.onEvent {
    jqueryEvent("filecleared") { _ ->
        console.log("File cleared.")
    }
}
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

The `kvision-state-flow` module, which depends on Kotlin coroutines library, allows you to easily create Flow streams of events. Using flows, you can process events from KVision components with the power of Kotlin flow API (e.g. handling back-pressure or combining multiple flows). The module gives you universal `eventFlow` extension function and the dedicated `clickFlow`, `inputFlow` and `changeFlow` functions for the corresponding event types.

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
