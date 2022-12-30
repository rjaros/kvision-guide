# Migration from 5.x to 6.x

This is the list of incompatibilities you may encounter when migrating your application to KVision 6.0.0.

## General changes

* Java 17 is now required to develop all KVision applications and to run KVision fullstack applications. You may need to select JDK 17 in the `File -> Project structure` menu in the IntelliJ IDEA.
* The legacy backend is no longer supported - be sure you are using IR.
* The fluent pattern has been removed from many components and containers which means some methods no longer return the component instance. Most notably the `onClick` method now returns handler id (like all other event methods). It could be a problem if you directly use `add()` methods instead of DSL.

```kotlin
// Works in KVision 5, doesn't work in KVision 6
add(Button("OK").onClick {
    // do something
})

// Works is KVision 5 and KVision 6
button("OK").onClick {
    // do something
}
```

## Modules and components

* `Spinner` and `Range` components have been moved to the new `io.kvision.form.number` package.
* `Select`, `Spinner` and `Upload` are now different components. The old `Upload` component based on bootstrap-fileinput library is still available as `BootstrapUpload`.  The old `Select` and `Spinner` component are no longer available. If you need advanced features use new `TomSelect` , `Spinner`, `Numeric` and `IMaskNumeric`  components.
* The `Spinner` component is now only for integer values. Use `Numeric` or `ImaskNumeric` components for decimal/float values.
* `Typeahead` component is no longer available. Use `TomTypeahead` from `kvision-tom-select` module instead.
* The `kvision-bootstrap-css` and `kvision-bootstrap-dialog` modules have been removed and are no longer necessary. `BootstrapCssModule` initializer works like before but is contained in the main `kvision-bootstrap` module.
* When using `Tabulator` component you need to use one of available css initializers. Use `TabulatorCssBootstrapModule` initializer for Bootstrap 5 theme.
* The `serializer` parameter is now required when initializing `Tabulator` with Kotlin data model. In most cases it should be enough to use `serializer = serializer()`. Of course your data model must be `@Serializable` now.
* The Navigo 7 router from `kvision-routing-navigo` module uses `#` as the default hash sign. Use `Routing.init(hash = "#!")` if you want to restore the previous behaviour.
* The `routing` global variable has been removed. The internal router instance is returned by the `Routing.init()` method.
* The `kvision-toast` module is no longer available. Use `kvision-toastify` instead with `ToastifyModule` initializer. The new `Toast` component has a bit different API (e.g. use `danger()` method instead of `error()`)

## Other changes

* The `getElement()` method returns `HTMLElement` type instead of `Node`. In some rare cases your code may not compile until the type is changed.
* The Cordova KVision API uses now `kotlin.Result<T>` instead of a self-implemented `Result` class. You need to adjust your code if you develop Cordova applications with KVision.
* The `panelsCompatibilityMode` option (compatibility with KVision 4) has been removed.
* Lots of old, deprecated API has been removed. Be sure you to fix all your deprecation warnings before upgrading to KVision 6.
