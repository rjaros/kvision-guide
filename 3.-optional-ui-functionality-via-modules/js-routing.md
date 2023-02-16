# JS Routing with Navigo

KVision supports JavaScript routing with optional modules since version 4.1.0. Two modules are available `kvision-routing-navigo` , based on [Navigo 7](https://github.com/krasimir/navigo/blob/master/README\_v7.md) library (for compatibility with earlier KVision versions) and `kvision-routing-navigo-ng` , based on current [Navigo 8+](https://github.com/krasimir/navigo).

Add the following in your `build.gradle.kts` file

Navigo 7: `implementation("io.kvision:kvision-routing-navigo:$kvisionVersion")`

Navigo 8+: `implementation("io.kvision:kvision-routing-navigo-ng:$kvisionVersion")`

{% hint style="info" %}
The following examples are using `kvision-routing-navigo-ng` module.
{% endhint %}

## Initializing

Call the `Routing.init()` method at the beginning of your `Application.start()` method.

```kotlin
override fun start() {
    val routing = Routing.init()
    // ...
}
```

By default, the router uses hash-based routing and sets the strategy equal to "ONE". This means that when a match is found, the router stops resolving other routes. Optional parameters of `init()` allow you to fully configure Navigo instance.

```kotlin
override fun start() {
    val routing = Routing.init("/app", useHash = false, strategy = Strategy.ALL)
    // ...
}
```

The `init()` function returns the configured router object, which can be used to call all [Navigo API](https://github.com/krasimir/navigo/blob/master/DOCUMENTATION.md) methods.

## Adding a route

To add a route, use the `on` method of the router object, which can take different different parameters.

```kotlin
routing
    .on("/foo", { console.log("Foo Page") }) // Default
    .on({ console.log("Main page") }) // omitting the url parameter defaults to the root url
    .on(RegExp("^test2/(.*)/(.*)/(.*)"), { match ->
        console.log("Regexp: ${match.data[0]} ${match.data[1]} ${match.data[2]}") }) //match with regex
    .resolve()
```

In all of these approaches, you pass in a function that is invoked when the route is matched

Resolve has to be called at least once. The method does the following

* Searches for a url match
* If yes, it calls [hooks](https://github.com/krasimir/navigo/blob/master/DOCUMENTATION.md#hooks) and your route handler
* updates the router's internal state

(Note, if you set strategy = "ALL", it will keep searching)

## Navigating

To navigate to a certain url, use the `navigate` method on the router object.

```kotlin
routing.navigate("/foo")
```

## Hooks

[Hooks](https://github.com/krasimir/navigo/blob/master/DOCUMENTATION.md#hooks) are functions that are fired during the resolving process. There are four hooks that are fired:

* before
* after
* leave
* already

You can override hooks for all routes, e.g.

```kotlin
routing.hooks(obj<RouteHooks> {
    before = { done, _ ->
        console.log("someBeforeHookLogic")
        done()
    }
})
```

or specific routes

```kotlin
routing.addBeforeHook("/foo") {
    console.log("someBeforeHookLogic")
    it()
}
```

## Usage with Panel

The JS router support is also directly available in the `TabPanel` and `StackPanel` containers. This way you can easily bind different parts of your application GUI with distinct URL addresses and make the "Back" button of the browser work as expected.

```kotlin
tabPanel {
    addTab("Basic formatting", BasicTab(), "fas fa-bars", route = "/basic")
    addTab("Forms", FormTab(), "fas fa-edit", route = "/forms")
    addTab("Buttons", ButtonsTab(), "far fa-check-square", route = "/buttons")
    addTab("Dropdowns & Menus", DropDownTab(), "fas fa-arrow-down", route = "/dropdowns")
    addTab("Containers", ContainersTab(), "fas fa-database", route = "/containers")
    addTab("Layouts", LayoutsTab(), "fas fa-th-list", route = "/layouts")
    addTab("Modals", ModalsTab(), "fas fa-window-maximize", route = "/modals")
    addTab("Data binding", DataTab(), "fas fa-retweet", route = "/data")
    addTab("Windows", WindowsTab(), "fas fa-window-restore", route = "/windows")
    addTab("Drag & Drop", DragDropTab(), "fas fa-arrows-alt", route = "/dragdrop")
}
```

{% hint style="info" %}
When using routing built into `TabPanel` or `StackPanel` components and also declaring manual routing with the same routes, remember to change the routing strategy from the default `Strategy.ONE` to `Strategy.ALL`.  &#x20;
{% endhint %}
