# Migration from 4.x to 5.x \(work in progress\)

This is the list of incompatibilities you may encounter when migrating your application to KVision 5.0.0.

## General changes

* `classes: Set<String>` constructor parameter and DSL builder parameter for all components has been replaced with `className: String?` parameter \(previously available only for DSL builders\), taking a space-separated list of CSS class names.

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

*  All DSL builder functions with `ObservableState` parameter has been removed. Use `bind()` extension function \(from the new `kvision-state` module\) instead.

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

* `FlexPanel`, `HPanel`, `VPanel` and `GridPanel` containers don't render wrapper DIVs by default for their children components. The previously available `noWrappers` parameter has been removed and new `useWrappers` \(defaults to `false`\) parameter has been introduced \(note the opposite meaning!\). To make migrations easier, a compatibility parameter `panelsCompatibilityMode`is available within `startApplication()` function. When set to `true`, the `useWrappers` default value will be `true` \(and you will only need to replace `noWrappers = true` with `useWrappers = false` to migrate your whole app\).

```kotlin
fun main() {
    startApplication(::App, module.hot, panelsCompatibilityMode = true)
}
```

* The default values for `Root` container parameters have been changed. Previously `container-fluid` was the default class applied and additional `row` was generated. In KVision 5 `Root` container doesn't generate any additional markup by default. 

