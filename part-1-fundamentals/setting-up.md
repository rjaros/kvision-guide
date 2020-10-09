# Setting up

KVision applications are built with [Gradle](http://gradle.org/). The official Kotlin/JS gradle plugin is used to manage NPM dependencies, pack bundles \(via [webpack](https://webpack.github.io/)\) and test the application using [Karma](http://karma-runner.github.io/1.0/index.html). By using Gradle continuous build, you also can get hot module replacement feature \(apply code changes in the browser on the fly\).

## Requirements

To build a typical KVision application you should have some tools installed on your machine and available on the system PATH:

* [JDK](https://jdk.java.net/) 8 or higher
* [Git](https://git-scm.com) \(with additional UNIX tools if using Windows\)
* GNU [xgettext](https://www.gnu.org/software/gettext) and [msgmerge](https://www.gnu.org/software/gettext) utilities to use [Internationalization](internationalization.md) features    

{% hint style="info" %}
Note: Make sure you are building KVision applications on the local file system.  
{% endhint %}

## Creating a new application

The recommended way to create a new application is to download and copy the [KVision template](https://github.com/rjaros/kvision-examples/tree/master/template) project, available on GitHub.

### build.gradle.kts

The `build.gradle.kts` file is responsible for the definition of the build process. It declares necessary repositories \(e.g. external bintray repositories\) and all required dependencies. It declares `zip` task, typically used to build distribution pack.

{% code title="build.gradle.kts" %}
```kotlin
import org.jetbrains.kotlin.gradle.targets.js.nodejs.NodeJsRootPlugin
import org.jetbrains.kotlin.gradle.targets.js.webpack.KotlinWebpack
import org.jetbrains.kotlin.gradle.targets.js.webpack.KotlinWebpackConfig

plugins {
    val kotlinVersion: String by System.getProperties()
    id("kotlinx-serialization") version kotlinVersion
    kotlin("js") version kotlinVersion
}

version = "1.0.0-SNAPSHOT"
group = "com.example"

repositories {
    mavenCentral()
    jcenter()
    maven { url = uri("https://dl.bintray.com/kotlin/kotlin-eap") }
    maven { url = uri("https://kotlin.bintray.com/kotlinx") }
    maven { url = uri("https://dl.bintray.com/kotlin/kotlin-js-wrappers") }
    maven { url = uri("https://dl.bintray.com/rjaros/kotlin") }
    mavenLocal()
}

// Versions
val kotlinVersion: String by System.getProperties()
val kvisionVersion: String by System.getProperties()

val webDir = file("src/main/web")

kotlin {
    target {
        browser {
            runTask {
                outputFileName = "main.bundle.js"
                sourceMaps = false
                devServer = KotlinWebpackConfig.DevServer(
                    open = false,
                    port = 3000,
                    proxy = mapOf(
                        "/kv/*" to "http://localhost:8080",
                        "/kvws/*" to mapOf("target" to "ws://localhost:8080", "ws" to true)
                    ),
                    contentBase = listOf("$buildDir/processedResources/js/main")
                )
            }
            webpackTask {
                outputFileName = "main.bundle.js"
            }
            testTask {
                useKarma {
                    useChromeHeadless()
                }
            }
        }
    }
    sourceSets["main"].dependencies {
        implementation("pl.treksoft:kvision:$kvisionVersion")
        implementation("pl.treksoft:kvision-bootstrap:$kvisionVersion")
        implementation("pl.treksoft:kvision-bootstrap-css:$kvisionVersion")
        implementation("pl.treksoft:kvision-bootstrap-datetime:$kvisionVersion")
        implementation("pl.treksoft:kvision-bootstrap-select:$kvisionVersion")
        implementation("pl.treksoft:kvision-bootstrap-spinner:$kvisionVersion")
        implementation("pl.treksoft:kvision-bootstrap-upload:$kvisionVersion")
        implementation("pl.treksoft:kvision-bootstrap-dialog:$kvisionVersion")
        implementation("pl.treksoft:kvision-fontawesome:$kvisionVersion")
        implementation("pl.treksoft:kvision-i18n:$kvisionVersion")
        implementation("pl.treksoft:kvision-richtext:$kvisionVersion")
        implementation("pl.treksoft:kvision-handlebars:$kvisionVersion")
        implementation("pl.treksoft:kvision-datacontainer:$kvisionVersion")
        implementation("pl.treksoft:kvision-redux:$kvisionVersion")
        implementation("pl.treksoft:kvision-chart:$kvisionVersion")
        implementation("pl.treksoft:kvision-tabulator:$kvisionVersion")
        implementation("pl.treksoft:kvision-pace:$kvisionVersion")
        implementation("pl.treksoft:kvision-moment:$kvisionVersion")
        implementation("pl.treksoft:kvision-toast:$kvisionVersion")
    }
    sourceSets["test"].dependencies {
        implementation(kotlin("test-js"))
        implementation("pl.treksoft:kvision-testutils:$kvisionVersion:tests")
    }
    sourceSets["main"].resources.srcDir(webDir)
}

fun getNodeJsBinaryExecutable(): String {
    val nodeDir = NodeJsRootPlugin.apply(project).nodeJsSetupTaskProvider.get().destination
    val isWindows = System.getProperty("os.name").toLowerCase().contains("windows")
    val nodeBinDir = if (isWindows) nodeDir else nodeDir.resolve("bin")
    val command = NodeJsRootPlugin.apply(project).nodeCommand
    val finalCommand = if (isWindows && command == "node") "node.exe" else command
    return nodeBinDir.resolve(finalCommand).absolutePath
}

tasks {
    create("generatePotFile", Exec::class) {
        dependsOn("compileKotlinJs")
        executable = getNodeJsBinaryExecutable()
        args("$buildDir/js/node_modules/gettext-extract/bin/gettext-extract")
        inputs.files(kotlin.sourceSets["main"].kotlin.files)
        outputs.file("$projectDir/src/main/resources/i18n/messages.pot")
    }
}
afterEvaluate {
    tasks {
        getByName("processResources", Copy::class) {
            dependsOn("compileKotlinJs")
            exclude("**/*.pot")
            doLast("Convert PO to JSON") {
                destinationDir.walkTopDown().filter {
                    it.isFile && it.extension == "po"
                }.forEach {
                    exec {
                        executable = getNodeJsBinaryExecutable()
                        args(
                            "$buildDir/js/node_modules/gettext.js/bin/po2json",
                            it.absolutePath,
                            "${it.parent}/${it.nameWithoutExtension}.json"
                        )
                        println("Converted ${it.name} to ${it.nameWithoutExtension}.json")
                    }
                    it.delete()
                }
            }
        }
        create("zip", Zip::class) {
            dependsOn("browserProductionWebpack")
            group = "package"
            destinationDirectory.set(file("$buildDir/libs"))
            val distribution =
                project.tasks.getByName("browserProductionWebpack", KotlinWebpack::class).destinationDirectory!!
            from(distribution) {
                include("*.*")
            }
            from(webDir)
            duplicatesStrategy = DuplicatesStrategy.EXCLUDE
            inputs.files(distribution, webDir)
            outputs.file(archiveFile)
        }
    }
}
```
{% endcode %}

### Source code

The source code for the application is contained in `src/main` directory. It consists of Kotlin sources in `kotlin` directory, optional `resources` \(e.g. images, CSS files, Handlebars templates, translation files\), and main `index.html` file in a `web` directory.

Test sources are contained in `src/test` directory.

### The Application class

The main KVision application class must extend the `pl.treksoft.kvision.Application` class and override the`start` method.

{% code title="App.kt" %}
```kotlin
class App : Application() {

    override fun start() {
        root("kvapp") {
            // TODO
        }
    }
}

fun main() {
    startApplication(::App, module.hot)
}
```
{% endcode %}

## Development

To run the application with Gradle continuous build, enter:

```text
./gradlew -t run                                    (on Linux)
gradlew.bat -t run                                  (on Windows)
```

After Gradle finishes downloading dependencies and building the application, open [http://localhost:3000/](http://localhost:3000/) in your favorite browser.

You can import the project in **IntelliJ IDEA** and open `src/main/kotlin/com/example/App.kt` file. You can of course use your favorite text editor.

Add some code inside the `start` function:

{% code title="App.kt" %}
```kotlin
override fun start() {
    // ...
    root("kvapp") {
        span("Hello world!")
    }
 }
```
{% endcode %}

You should see your changes immediately in the browser.

{% hint style="info" %}
It's recommended to use Gradle from a command line \(in a terminal window\). You may encounter problems if you run build tasks directly from IntelliJ IDEA.
{% endhint %}

## Production

To build a complete application optimized for production, run:

```text
./gradlew clean zip                   (on Linux)
gradlew.bat clean zip                 (on Windows)
```

The package containing all of application files will be saved as `build/libs/template-1.0.0-SNAPSHOT.zip`.

## Debugging

You can easily debug KVision applications in most modern browsers \(e.g. Chrome or Firefox\), which support source maps generated by Kotlin/JS compiler. Source maps allow you to see Kotlin source code in the browser "Sources" panel. You can see line numbers, step through your code, set breakpoints and watch variable values just like with plain JavaScript.

{% hint style="info" %}
Note: Source maps are disabled by default in all KVision template projects, because the development cycle \(hot reload\) is a lot faster without them. To enable source maps you need to remove line `sourceMaps = false` from your `build.gradle.kts` file and remove line `config.devtool = 'eval-cheap-source-map'` from `webpack.config.d/webpack.js` file.
{% endhint %}

