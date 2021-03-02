# JS routing

KVision supports JavaScript routing with optional modules since version 4.1.0. Two modules are available `kvision-routing-navigo` , based on [Navigo 7](https://github.com/krasimir/navigo/blob/master/README_v7.md) library \(for compatibility with earlier KVision versions\) and `kvision-routing-navigo-ng` , based on current [Navigo 8+](https://github.com/krasimir/navigo). 

To use JS routing you need to add one of the modules in your `build.gradle.kts` file and call `Routing.init()` method at the beginning of your `Application.start()` method. 

```kotlin
override fun start() {
    Routing.init()
    // ...
}
```

By default the router is configured for hash-based routing. Optional parameters of `init()` allow you to fully configure Navigo instance. 

```kotlin
override fun start() {
    Routing.init("/app", useHash = false, strategy = Strategy.ALL)
    // ...
}
```

The router object is available as a global variable `routing` in the `io.kvision.routing` package, and all [Navigo API](https://github.com/krasimir/navigo/blob/master/DOCUMENTATION.md) methods can be called directly.

```kotlin
routing.on({ println("Main page") })
    .on("/test", { println("Test route") })
    .on(RegExp("^test2/(.*)/(.*)/(.*)"), { match -> 
        println("Regexp: ${match.data[0]} ${match.data[1]} ${match.data[2]}") 
    }).resolve()
```

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

