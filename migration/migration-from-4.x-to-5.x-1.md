# Migration from 4.x to 5.x

This is the list of incompatibilities you may encounter when migrating your application to KVision 5.0.0.

## General changes

* All `classes: Set<String>` constructor parameters and DSL builder parameters for all KVision components have been replaced with `className: String?` parameters \(previously available only for DSL builders\), taking a space-separated list of CSS class names.

```kotlin
// KVision 4

div(classes = setOf("card", "important")) {
   + "Some content"
} 

// KVision 5

div(className = "card important") {
   + "Some content"
} 
```

*  All DSL builder functions with `ObservableState` parameter have been removed. Use `bind()` extension function instead \(from the new `kvision-state` module\).

```kotlin
val state = ObservableValue("Centered content")

// KVision 4

div(state, align = Align.CENTER) { 
    p(it)
}

// KVision 5

div(align = Align.CENTER).bind(state) {
    p(it)
}
```

{% hint style="info" %}
Note: You can try to use `bindSync()` extension function in case of very rare timing problems.
{% endhint %}

* `FlexPanel`, `HPanel`, `VPanel` and `GridPanel` containers don't render wrapper DIVs by default for their children components. The previously available `noWrappers` parameter has been removed and new `useWrappers` \(defaults to `false`\) parameter has been introduced \(note the opposite meaning!\). To make migrations easier, a compatibility parameter `panelsCompatibilityMode`is available within `startApplication()` function. When set to `true`, the `useWrappers` default value will be `true`, and you will only need to replace `noWrappers = true` with `useWrappers = false` to migrate your whole app.

```kotlin
fun main() {
    startApplication(::App, module.hot, panelsCompatibilityMode = true)
}
```

* The default values for `Root` container parameters have been changed. Previously `container-fluid` was the default CSS class applied and additional element with `row` class was also generated. In KVision 5 `Root` container by default doesn't render any additional markup.

```kotlin
// KVision 4

root("kvapp", containerType = ContainerType.NONE, addRow = false) {
    // ...
}

// KVision 5

root("kvapp") {
    // ...
}
```

* Bootstrap 5 introduces many breaking changes. You should definitely read [Bootstrap's migration guide](https://getbootstrap.com/docs/5.1/migration/) if you use Bootstrap CSS classes directly.
* Micronaut 3.0 introduces some breaking changes. Check out [Micronaut upgrading guide](https://docs.micronaut.io/3.0.0/guide/index.html#upgrading).
* Javalin 4.0 introduces some breaking changes. Check out the [changelog](https://javalin.io/news/javalin-4-release-candidate).

## Modules

* All modules which include CSS stylesheets require explicit initialization. This also applies to the core module. This initialization ensures a predictable order in which all styles will be applied. The initialization is performed by adding dedicated module objects as parameters to the `startApplication()` function.

```kotlin
fun main() {
    startApplication(
        ::App,
        module.hot,
        BootstrapModule,
        BootstrapCssModule,
        FontAwesomeModule,
        BootstrapSelectModule,
        BootstrapDatetimeModule,
        BootstrapUploadModule,
        CoreModule
    )
}
```

* These methods: `getElementJQuery()`, `getElementJQueryD()`, `showAnim()`, `hideAnim()`, `slideDown()`, `slideUp()`, `fadeIn()`, `fadeOut()` and `animate()` of the `Widget` class have been extracted to the new `kvision-jquery` module as the extension functions.
* Other jQuery dependencies \(including static `jQuery()` function\) have been moved to the `kvision-jquery` module as well. The core module no longer depends on jQuery library.
* The `RestClient` component has been moved to the new `kvision-rest` module and completely redesigned \(including its API\). To make migrations easier, the original, jQuery based client has been moved to the `kvision-jquery` module as a deprecated `LegacyRestClient`.
* The `ObservableList` and `ObservableSet` classes and data bindings functions `bind()`, `bindEach()` and `bindTo()` have been moved to the `kvision-state` module.
* The `Table` component has been moved to the `kvision-bootstrap` module.
* The `kvision-event-flow` module has been renamed to `kvision-state-flow`.

## Events

KVision no longer re-dispatches native component events as custom events. Instead it gives you the possibility to add direct listeners for all events with `event()` and `jqueryEvent()` extension functions, used inside `onEvent` function.

### Standard events

For standard events, just use the name.

```kotlin
text.onEvent {
    change = {
        console.log("Input text changed.")
    }
}
```

### KVision custom events

For custom KVision events \(defined by `SplitPanel`, `Window`, `Tabulator`, `TabPanel` and some OnsenUI components\) use the name of the event as well. Note: events for `Tabulator` and `TabPanel` component have been renamed \(e.g. `tabulatorRowClick` -&gt; `rowClickTabulator`\).

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

###  Bootstrap custom events

For events defined by Bootstrap components, which cannot be used like above because of their names, use `event()` extension function.

```kotlin
// KVision 4

modal.onEvent {
    shownBsModal = {
        console.log("Bootstrap modal is shown.")
    }
    hiddenBsModal = {
        console.log("Bootstrap modal is hidden")
    }
}

// KVision 5

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

For events defined by jQuery based components \(`Select`, `Spinner`, `DateTime`, `Typeahead`, `Upload`\) use `jqueryEvent()` extension function.

```kotlin
// KVision 4

select(listOfPairs("Option 1", "Option 2", "Option 3")) {
    onEvent {
        changedBsSelect = {
            console.log("Selection changed.")
        }
    }
}

// KVision 5

select(listOfPairs("Option 1", "Option 2", "Option 3")) {
    onEvent {
        jqueryEvent("changed.bs.select") { _ ->
            console.log("Selection changed.")
        }
    }
}
```

## Other changes

* The `SplitPanel` container is now sized with percentage values. You can use `minWidth` and/or `maxWidth` properties for the child component to block automatic resizing.
* Due to Bootstrap 5 limitation, components can't have both tooltip and popover enabled at the same time.
* `Tabulator` component no longer uses table types enums of the `Table` component. It now has its own enum values in its own `kvision-tabulator` module.
* `Style` objects have been refactored. The order of constructor parameters has changed. The `className` parameter has been renamed to `selector` to avoid confusion.
* The KVision Gradle plugin ID has changed from `kvision` to `io.kvision` and maven coordinates of the plugin artifacts have changed to comply with Gradle conventions. The `resolutionStrategy` block in `settings.gradle.kts` file is no longer required for KVision plugin. The plugin is now used with frontend-only projects as well, to simplify build file configuration. 

