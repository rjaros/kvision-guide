# Migration

This is the list of incompatibilities you may encounter when migrating your application to KVision 4.5.0:

* You need to upgrade Kotlin to 1.5.0 and serialization to 1.2.0 in your project `gradle.properties`. 
* Webpack used internally by Kotlin/JS plugin was upgraded to 5.x. You need to adjust any custom configuration in your `webpack.config.d` directory. Please use these [bootstrap.js](https://raw.githubusercontent.com/rjaros/kvision-examples/master/template/webpack.config.d/bootstrap.js), [css.js](https://raw.githubusercontent.com/rjaros/kvision-examples/master/template/webpack.config.d/css.js) and [file.js](https://raw.githubusercontent.com/rjaros/kvision-examples/master/template/webpack.config.d/file.js) files as examples. You can find more information [here](https://webpack.js.org/migrate/5/).
* `kvision-onsenui-css` module was removed and integrated with `kvision-onsenui` module.
* When developing full-stack application with Spring Boot 2.4.5, you need to add this snippet to your `build.gradle.kts`:

```text
extra["kotlin.version"] = "1.5.0"
extra["kotlin-coroutines.version"] = "1.5.0-RC"
```

This is the list of incompatibilities you may encounter when migrating your application to KVision 4.1.0:

* DSL builder functions have been annotated with `@DslMarker` annotation, which provides [strict scope control](https://kotlinlang.org/docs/type-safe-builders.html#scope-control-dslmarker) over implicit receivers. It can lead to compile errors for existing code with hard to notice bugs. Sometimes it may also be necessary to specify the receivers explicitly.
* Routing functionality has been externalized to optional modules. If your application uses built-in KVision routing, you need to add `kvision-routing-navigo` module to your dependencies and call `Routing.init()` at the beginning of your `Application.start()` method.

This is the list of incompatibilities you may encounter when migrating your application to KVision 4.0.0:

* Artifacts publication has been moved to Maven Central under new maven coordinates with `io.kvision` group identifier. You have to update all KVision dependencies in your `build.gradle.kts`.
* All package names have been renamed with `io.kvision` prefix. You should replace your sources in the following order:
  * `pl.treksoft.navigo` with `io.kvision.navigo`
  * `pl.treksoft.jquery` with `io.kvision.jquery`
  * `pl.treksoft.kvision` with `io.kvision`
* Make sure you are not using any deprecated code before migrating. All methods, classes and functions deprecated before version 4.0.0 have been removed.
* If you use web sockets with Ktor backend you need to manually install WebSockets feature in your `main()` function.
* If you use Ktor backend you should move `kvisionInit()` call to the end of your `main()` function.
* If you use `moment` module with locale support you need to manually `require()` all or some of the needed locales. You should also update `moment.js` file in the `webpack.config.d` directory from the current template.
* You should remove all Bintray repository addresses from your Gradle build files.

This is the list of incompatibilities you may encounter when migrating your application to KVision 3.13.x:

* Due to changes in the Kotlin/JS gradle plugin for Kotlin 1.4.0 it is necessary to fix your `build.gradle.kts` file and `webpack.config.d/webpack.js` file. You can apply the following patch for `build.gradle.kts` file and just copy new version of `webpack.js` from the current template project \(standard or fullstack, respectively\).

{% file src="../.gitbook/assets/kvision-3.13.0.txt" caption="Patch for build.gradle.kts file" %}

* `Kotlinx.serialization` library 1.0.0 contains a lot of [incompatible changes](https://github.com/Kotlin/kotlinx.serialization/releases/tag/1.0.0-RC), so you have to migrate your code if you are using this library directly. 
* `Jed` was replaced by `gettext.js` library. You can safely delete `webpack.config.d/jed.js` file. You need to make sure your `*.po` files contain both `Language` and `Plural forms` headers.
* All flexbox and grid CSS properties and enums have been moved to the `StyledComponent` and the core package. Deprecated type aliases for refactored enums were added. Follow deprecation messages to remove deprecated types from your code.
* If you are using fullstack configuration with Spring Boot add `extra["kotlin.version"] = "1.4.0"` to your `build.gradle.kts` file.

This is the list of incompatibilities you may encounter when migrating your application to KVision 3.5.x:

* Due to changes in the Kotlin/JS gradle plugin it is necessary to fix your `build.gradle.kts` file and `webpack.config.d/webpack.js` file. Carefully analyze [these changes](https://github.com/rjaros/kvision-examples/compare/9a63de5933fd0ac385b5b41468c5006176407aa1..0dd57450cc37350780ea0febcf12fcdb90b3fe37#diff-0577060241e9967978e7e7039df0646c) for frontend projects and [these changes](https://github.com/rjaros/kvision-examples/compare/9a63de5933fd0ac385b5b41468c5006176407aa1..0dd57450cc37350780ea0febcf12fcdb90b3fe37#diff-c6a77204309bf123278dd17c72f0b725) for fullstack projects.
* `Kotlinx.serialization` library 0.20.0 contains also some [incompatible changes](https://github.com/Kotlin/kotlinx.serialization/blob/master/CHANGELOG.md#0200--2020-03-04), so you have to migrate your code if you are using this library directly.

This is the list of incompatibilities you may encounter when migrating your application to KVision 3:

* The `setEventListener()` method and the `onEvent()` extension function return now an `Int` instead of a `Widget`. The returned value can be used with `removeEventListener(id: Int)` method.
* The old, deprecated `setEventListener()` method without type parameter has been removed. Use `onEvent` instead.
* Problematic overloaded constructors for all css styling classes in the `core` package - `Color`, `Border`, `Background`, `TextDecoration and` `TextShadow` has been removed. Each class has only one, primary constructor.
* There are two new factory extension functions for the `Color` class. `Color.hex()` allows you to create a `Color` object from a hexadecimal `Int` literal. `Color.name()` allows you to create a `Color` object with an `Col` enum value.
* The dependency on the Pac4J library has been removed. The `p.t.k.remote.Profile` class has been removed as well. You can add Pac4J to your own project if you still want to use it.
* Jooby has been updated to version 2.x. You need to upgrade your application if you are using Jooby integration. See [upgrade docs](https://jooby.io/#appendix-upgrading-from-x).

This is the list of incompatibilities you may encounter when migrating your application to KVision 2:

* All DSL builder functions have been moved out of the companion objects to allow better auto-completion in IntelliJ IDEA. This change is incompatible with KVision 1 code. To migrate you should just remove code based on this regular expression: `[^\.]+\.Companion\.` from all your frontend code imports \(Kotlin compiler will help you easily find all possible errors afterwards\).
* The default font size in Bootstrap 4 is larger. You may have to adjust your application layout manually or use a [custom CSS theme](themes.md).
* The direct child of the `Root` component is no longer 100% wide. Use `width = 100.perc` if you want it to be.
* The modules names have changed \(the API of the components, package names, class names, methods and properties are mostly the same, with the differences described in details below\).

| KVision 1 module name | KVision 2+ module name |
| :--- | :--- |
| kvision-select | kvision-bootstrap-select |
| kvision-select-remote | kvision-bootstrap-select-remote |
| kvision-datetime | kvision-bootstrap-datetime |
| kvision-spinner | kvision-bootstrap-spinner |
| kvision-upload | kvision-bootstrap-upload |
| kvision-dialog | kvision-bootstrap-dialog |

* Two new modules: `kvision-bootstrap-css` and `kvision-fontawesome` were extracted from the `kvision-bootstrap` module.
* The Glyphicons support is dropped. Use Font Awesome icons instead.
* The Font Awesome icon names require a style prefix \(`fas`, `far` or `fab`\).
* Components like dropdown, context menu, modal, window, progressbar, navbar, toolbar, responsive grid, tab panel, tooltip and popover were moved into `kvision-bootstrap` module.
* `ButtonStyle.DEFAULT` was removed. The `ButtonStyle.PRIMARY` value is the new default. New `ButtonStyle.SECONDARY` value is also available.
* `RadioStyle.DEFAULT` and `CheckBoxStyle.DEFAULT` were removed and are no longer necessary.
* The deprecated `Label` component was removed. Use `Span` component instead.
* `TableType.CONDENSED` was removed. Use new `TableType.SMALL` value instead.
* `ButtonGroupStyle.JUSTIFIED` was removed. Use a [different approach](https://getbootstrap.com/docs/4.0/migration/#button-group).
* The `responsive` property of the `Table` component was removed. Use `responsiveType` property with a chosen enum value instead.
* The `dropup` property of the `DropDown` component was removed. Use the`direction` property with a `Direction.DROPUP` value instead.
* The `withCaret` parameter of the `DropDown` component constructor was removed. No replacement at the moment.
* `NavbarType.STATICTOP` was removed. Use `NavbarType.STICKYTOP` instead.
* Do not use `Tag(TAG.LI)` components when creating links inside `Nav` component. Use `Link` with `nav-item` and `nav-link` classes \(or use `navLink` DSL builder function\).
* When adding links to the `DropDown` or `ContextMenu` use `Link` with a `dropdown-item` class \(or use `ddLink` and `cmLink` DSL builder functions respectively\).
* `GridSize.XS` was removed from the `ResponsiveGridPanel` component.
* The `buttonsType` is no longer a var property in the `Spinner` and the `SpinnerInput` components \(changing its value didn't work anyway ;-\)
* The `DateTime` and `DateTimeInput` components are based on a new version of bootstrap-datetimepicker. The `weekstart`, `todayhighlight` and`showmeridian` properties were removed. The `todayBtn` and `clearBtn` properties were renamed to `showTodayButton` and `showClear` respectively. 
* The `ObservableList` class was moved from `pl.treksoft.kvision.utils` to `pl.treksoft.kvision.state` package.
* The `StateBinding` component was moved from `pl.treksoft.kvision.redux` to `pl.treksoft.kvision.state` package and is available in the main KVision module. It also can be used with any data source implementing `ObservableState` interface \(e.g. `ReduxStore` or `ObservableList`\).
* The mapping of date and time classes between the client and the server code was significantly changed. The `pl.treksoft.kvision.types.Date` class is deprecated and not supported anymore. You should use `LocalDateTime`, `LocalDate`, `LocalTime`, `OffsetDateTime` and `OffsetTime` from `pl.treksoft.kvision.types` package instead. All of these classes are mapped to `kotlin.js.Date` on the client side, but to the corresponding `java.time.*` class on the server side. This way you can easily use new Java types in your server side applications.
* The `pl.treksoft.kvision.hmr` package was removed and is not needed anymore. All KVision applications should now extend `pl.treksoft.kvision.Application` class and are executed with new `startApplication()` function.
* `ReduxStore.subscribe()` method calls the callback function once immediately right after the registration.
* Service functions for `SelectRemote` / `TabulatorRemote` were changed to suspending. The function for `SelectRemote` component takes additional `state: String?` parameter.
* You are strongly encouraged to migrate your server-side interfaces common and frontend code with the help of the new KVision compiler plugin.

