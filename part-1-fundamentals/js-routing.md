# JS routing

KVision has a built-in support for the JavaScript router [Navigo](https://github.com/krasimir/navigo) by using a wrapper library [navigo-kotlin](https://github.com/rjaros/navigo-kotlin). The router object is available as a global variable `routing` in the `pl.treksoft.kvision.routing` package, and all [Navigo API](https://github.com/krasimir/navigo#api) methods can be called directly.

```kotlin
routing.on({ _ -> println("Main page") })
    .on("/test", { _ -> println("Test route") })
    .on(RegExp("/test3/(.*)/(.*)/(.*)"), { x, y, z -> println("Regexp: $x $y $z") })
    .resolve()
```

The JS router support is also directly available in the `TabPanel` and `StackPanel` containers. This way you can easily bind different parts of your application GUI with distinct URL addresses and make the "Back" button of the browser work as expected.

```kotlin
tabPanel {
    addTab("Basic formatting", BasicTab(), "fa-bars", route = "/basic")
    addTab("Forms", FormTab(), "fa-edit", route = "/forms")
    addTab("Buttons", ButtonsTab(), "fa-check-square-o", route = "/buttons")
    addTab("Dropdowns & Menus", DropDownTab(), "fa-arrow-down", route = "/dropdowns")
    addTab("Containers", ContainersTab(), "fa-database", route = "/containers")
    addTab("Layouts", LayoutsTab(), "fa-th-list", route = "/layouts")
    addTab("Modals", ModalsTab(), "fa-window-maximize", route = "/modals")
    addTab("Data binding", DataTab(), "fa-retweet", route = "/data")
    addTab("Windows", WindowsTab(), "fa-window-restore", route = "/windows")
    addTab("Drag & Drop", DragDropTab(), "fa-arrows-alt", route = "/dragdrop")
}
```
