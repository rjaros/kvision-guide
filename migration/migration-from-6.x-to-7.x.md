# Migration from 6.x to 7.x

This is the list of incompatibilities you may encounter when migrating your application to KVision 7.0.0.

* Gradle 8.2 or later is required. If you are still using older version or you are not sure you can just use `./gradlew wrapper --gradle-version latest --distribution-type all` command (`gradlew.bat` on Windows).
* `kotlin("js")` Gradle plugin is deprecated in Kotlin 1.9.0 and is no longer supported by KVision Gradle plugin. To migrate your frontend-only project to `kotlin("multiplatform")` plugin please follow [the official guide](https://kotlinlang.org/docs/multiplatform-compatibility-guide.html#migration-from-kotlin-js-gradle-plugin-to-kotlin-multiplatform-gradle-plugin). For a typical KVision application you just need to:
  * Rename directories `src/main` to `src/jsMain` and `src/test` to `src/jsTest`.
  * Replace `kotlin("js")` with `kotlin("multiplatform")` inside the `plugins` section of `build.gradle.kts` file.
  * Replace `sourceSets["main"]` with `sourceSets["jsMain"]` and `sourceSets["test"]` with `sourceSets["jsTest"]` in the `build.gradle.kts` file.
* To unify configuration of all KVision applications, the fullstack projects are now using standard source sets names `jsMain` and `jvmMain` (instead of `frontendMain` and `backendMain`). To migrate your KVision fullstack project:
  * Rename directories `src/frontendMain` to `src/jsMain`, `src/frontendTest` to `src/jsTest`, `src/backendMain` to `src/jvmMain` and `src/backendTest` to `src/jvmTest`.
  * Replace `jvm("backend")` to `jvm` and `js("frontend")` to `js(IR)` in the `build.gradle.kts` file.
  * Replace `frontendMain` to `jsMain` , `frontendTest` to `jsTest` , `backendMain` to `jvmMain` and `backendTest` to `jvmTest` in the `build.gradle.kts` file.
  * Replace `processedResources/frontend/main` to `processedResources/js/main` in the `build.gradle.kts` file.
  * Replace `processedResources/frontend/main` to `processedResources/js/main` in the `webpack.config.d/webpack.js` file.
* All custom Gradle tasks for fullstack applications are now integrated with KVision Gradle plugin. Unless you need some custom functionality you should remove all tasks declared after main `kotlin {}` block in your `build.gradle.kts` file (with the exception of server specific configuration for Jooby (`joobyRun {}` block), Vert.x (`vertx {}` block) and Kapt configuration for Micronaut). If you are using Javalin, Jooby or Ktor backend, you should also use new `kotlin { jvm { mainRun {} } }` block to configure mainClass name. For more details about migration fullstack apps check the corresponding template projects in the [kvision-examples](https://github.com/rjaros/kvision-examples) repository.
* The `selectSize` property of the `TomSelect` component was renamed to `maxOptions` to match the orginal JS component option name.
