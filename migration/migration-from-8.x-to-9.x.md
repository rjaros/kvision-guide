# Migration from 8.x to 9.x

KVision 9.0.0 is a major upgrade with changes affecting both functionalities and application structure. The goal of this release is to simplify the maintenance of the framework and allow me to continue supporting it. This required sacrificing several modules that, to my knowledge, were not widely (or even at all) used. At the same time, the KVision architecture was modernized, allowing for fixing issues and adding support for new features.

This is the list of incompatibilities you may encounter when migrating your application to KVision 9.0.0.

## General changes

* The `kvision-electron` , `kvision-cordova`  and `kvision-onsenui` modules were removed. If you use any of these modules, migration to KVision 9.x is not possible. Please let me know if this is your case.
* All code has been migrated to ES modules and `es2015` target. As a result the `require` function, used for importing resources and integrating NPM libraries, is no longer available. Use `@JsModule` annotation instead. For instance, if you use this code to import CSS and JSON files:

```kotlin
class App : Application() {
    init {
        require("css/kvapp.css")
    }
    override fun start() {
        I18n.manager =
                DefaultI18nManager(
                    mapOf(
                        "en" to require("i18n/messages-en.json"),
                        "pl" to require("i18n/messages-pl.json"),
                    )
                )
        // ...
    }
}
```

you should now use this:

```kotlin
import io.kvision.utils.useModule

@JsModule("/kotlin/modules/css/kvapp.css")
external val kvappCss: dynamic

@JsModule("/kotlin/modules/i18n/messages-en.json")
external val messagesEn: dynamic

@JsModule("/kotlin/modules/i18n/messages-pl.json")
external val messagesPl: dynamic

class App : Application() {
    init {
        useModule(kvappCss)
    }
    override fun start() {
        I18n.manager =
                DefaultI18nManager(
                    mapOf(
                        "en" to messagesEn,
                        "pl" to messagesPl,
                    )
                )
        // ...
    }
}
```

You need to move the resources processed by webpack (e.g. css files, images, po files or hbs templates) to the `src/jsMain/resources/modules` directory and use `/kotlin/modules` prefix, when importing them.

* Change `module.hot` to `js("import.meta.webpackHot").unsafeCast<Hot?>()` when using [HMR](../1.-getting-started-1/hot-module-replacement.md).
* Move the content of the `src/jsMain/web` directory to `src/jsMain/resources` . The `web` directory is no longer used.
* Add `useEsModules()` and `compilerOptions { target.set("es2015") }` to your `build.gradle.kts` file. Check [Creating new application](../1.-getting-started-1/creating-a-new-application.md) chapter for reference.
* Add `kotlin.js.ir.output.granularity=per-file` property to your `gradle.properties` file.
* When using `kvision-pace` module with the default theme, you need to provide a parameter to `Pace.init()` call. Use the code like this:

```kotlin
@JsModule("pace-progressbar/themes/blue/pace-theme-flash.css")
external val paceThemeFlash: dynamic

class App : Application() {
    init {
        Pace.init(paceThemeFlash)
    }
}
```

* Remove these two lines from `webpack.config.d/webpack.js` file:

```javascript
config.resolve.modules.push("../../processedResources/js/main");
config.resolve.conditionNames = ['import', 'require', 'default'];
```

## Fullstack changes

All `kvision-server-*` modules were removed and KVision by itself no longer provides fullstack interfaces. You have to migrate your application to use [Kilua RPC](https://github.com/rjaros/kilua-rpc) library. This library was created as a standalone project that modernizes concepts originally implemented in KVision.&#x20;

KVision still provides `kvision-common-remote` module, which supports date and time types declared in `io.kvision.types` package, so you don't have to change your application logic.

{% hint style="info" %}
Note: Kilua RPC temporarily doesn't support Micronaut server, because of dependencies conflicts and no KSP2 support.
{% endhint %}

To migrate your application:

* Add versions of KSP and Kilua RPC to your gradle.properties file:

```properties
systemProp.kspVersion=2.1.20-2.0.0
systemProp.kiluaRpcVersion=0.0.32
```

* Add two Gradle plugins to your project:

```kotlin
plugins {
    val kotlinVersion: String by System.getProperties()
    kotlin("plugin.serialization") version kotlinVersion
    kotlin("multiplatform") version kotlinVersion
    val kspVersion: String by System.getProperties()      // new
    id("com.google.devtools.ksp") version kspVersion      // new
    val kiluaRpcVersion: String by System.getProperties() // new
    id("dev.kilua.rpc") version kiluaRpcVersion           // new
    val kvisionVersion: String by System.getProperties()
    id("io.kvision") version kvisionVersion
}
```

* Remove `kvision-server-*` dependency from your common source set and add new dependencies on Kilua RPC (use `implementation()` instead of `api()` ):

```kotlin
val commonMain by getting {
    dependencies {
        implementation("dev.kilua:kilua-rpc-ktor-koin:$kiluaRpcVersion")
        implementation("io.kvision:kvision-common-remote:$kvisionVersion")
    }
}
```

* Kilua RPC doesn't support Guice, so if you are using it in your app, you should migrate to Koin or use manual bindings without any dependency injection, like this:

```kts
initRpc {
    registerService<IEncodingService> { EncodingService() }
}
```

* Replace annotations in your common code:
  * `io.kvision.annotations.KVService` to `dev.kilua.rpc.annotations.RpcService`
  * `io.kvision.annotations.KVBinding` to `dev.kilua.rpc.annotations.RpcBinding`&#x20;
  * `io.kvision.annotations.KVBindingMethod` to `dev.kilua.rpc.annotations.RpcBindingMethod`&#x20;
  * `io.kvision.annotations.KVBindingRoute` to `dev.kilua.rpc.annotations.RpcBindingRoute`&#x20;
  * `io.kvision.annotations.KVServiceException` to `dev.kilua.rpc.annotations.RpcServiceException`&#x20;
* Replace types:
  * &#x20;`io.kvision.types.Decimal` to `dev.kilua.rpc.types.Decimal`&#x20;
  * `io.kvision.remote.RemoteOption` to`dev.kilua.rpc.RemoteOption`&#x20;
  * `io.kvision.remote.SimpleRemoteOption` to`dev.kilua.rpc.SimpleRemoteOption`&#x20;
  * `io.kvision.remote.RemoteData` to`dev.kilua.rpc.RemoteData`&#x20;
  * `io.kvision.remote.RemoteFilter` to`dev.kilua.rpc.RemoteFilter`&#x20;
  * `io.kvision.remote.RemoteSorter` to`dev.kilua.rpc.RemoteSorter`&#x20;
  * `io.kvision.remote.RemoteSerialization` to`dev.kilua.rpc.RpcSerialization`&#x20;
  * `io.kvision.remote.ServiceException` to`dev.kilua.rpc.ServiceException`&#x20;
  * `io.kvision.remote.SecurityException` to`dev.kilua.rpc.SecurityException`&#x20;
  * `io.kvision.remote.ContentTypeException` to`dev.kilua.rpc.ContentTypeException`&#x20;
  * `io.kvision.remote.KVServiceManager` to`dev.kilua.rpc.RpcServiceManager`&#x20;
* Your service class in JVM sources set should no longer be `actual` . Remove `actual` keyword and `@Suppress("ACTUAL_WITHOUT_EXPECT")` annotation if you use it.
* Replace functions:
  * `io.kvision.remote.getServiceManager` to`dev.kilua.rpc.getServiceManager`&#x20;
  * `io.kvision.remote.getAllServiceManagers` to`dev.kilua.rpc.getAllServiceManagers`&#x20;
  * `io.kvision.remote.serviceMatchers` to`dev.kilua.rpc.serviceMatchers`&#x20;
  * `io.kvision.remote.getService` to`dev.kilua.rpc.getService`&#x20;
* Replace `kv_remote_url_prefix` configuration option with `rpc_url_prefix` .
* Add `registerRemoteTypes()` call to your `main()` functions in both `js` and `jvm` sources set.
* The production version of the application is now assembled with `jarWithJs` Gradle task.

### Spring Boot

* You need to modify the structure of your Spring Boot project, because Kotlin 2.1.20 no longer supports using Kotlin Multiplatform and Spring Boot Gradle plugin in the same module. You need to add additional `application` submodule. Please check [`addressbook-fullstack-spring-boot`](https://github.com/rjaros/kvision-examples/tree/master/addressbook-fullstack-spring-boot) example application for reference.
* Using `io.spring.dependency-management` Gradle plugin can cause problems with dependencies (see [KT-75643](https://youtrack.jetbrains.com/issue/KT-75643)). Use Spring BOM instead.
