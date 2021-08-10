# Migration from 4.x to 5.x \(work in progress\)

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

*  All DSL builder functions with `ObservableState` parameter has been removed. Use `bind()` extension function instead \(from the new `kvision-state` module\).

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

## Modules

* KVision 5 gives you a few new optional modules:
  * `kvision-jquery` - jQuery bindings. Contain extension functions which previously were methods of the `Widget` class. Also contain `LegacyRestClient` - original, jQuery based HTTP client.
  * `kvision-bootstrap-icons` - bindings for [Bootstrap Icons](https://icons.getbootstrap.com/) project.
  * `kvision-rest` - completely new `RestClient` component, based on browser fetch API.
  * `kvision-state` - observable data structures and data bindings.
  * `kvision-state-flow` \(renamed from `kvision-event-flow`\) - data bindings and extension functions to work with Kotlin coroutines `Flow`, `StateFlow` and `SharedFlow`.
* If you were using any of the `getElementJQuery()`, `getElementJQueryD()`, `showAnim()`, `hideAnim()`, `slideDown()`, `slideUp()`, `fadeIn()`, `fadeOut()`, animate\(\) methods, you need to include `kvision-jquery` module and import appropriate extension functions.
* If you were using `RestClient` component you can either include `kvision-rest` module and use new `RestClient` \(by adjusting your code to new API\) or include `kvision-jquery` module and use deprecated `LegacyRestClient` \(without any additional changes\).
* If you were using `ObservableList`, `ObservableSet` or data bindings with `bind()`, `bindEach()` or `bindTo()` functions, you need to include `kvision-state` module. The API is the same.
* All modules which include CSS stylesheets require explicit initialization. This also applies to the core module. This initialization ensures a predictable order in which all styles will be applied. The initialization is performed by adding module objects as parameters to the `startApplication()` function.

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

* `Table` component was moved to the `kvision-bootstrap` module.

 

