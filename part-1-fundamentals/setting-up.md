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

The recommended way to create a new application is to use [KVision Project Wizard](https://plugins.jetbrains.com/plugin/16533-kvision-project-wizard) plugin for IntelliJ IDEA. Just install the plugin from JetBrains Marketplace in your IDE and run `File -> New -> Project...` from the main menu. Choose `KVision` group, select desired project type and choose modules from the list on the first page. On the second page choose your project name and location. Your project will be generated and opened in IDE when you click `Finish` button. After a few moments required to download all required dependencies you are ready to go.

You can also download and copy the [KVision template](https://github.com/rjaros/kvision-examples/tree/master/template) project, available on GitHub.

### build.gradle.kts

The `build.gradle.kts` file is responsible for the definition of the build process. It declares all required dependencies \(in particular KVision optional modules\). KVision Gradle plugin is used to simplify the configuration. 

{% code title="build.gradle.kts" %}
```kotlin
import org.jetbrains.kotlin.gradle.targets.js.webpack.KotlinWebpackConfig

plugins {
    val kotlinVersion: String by System.getProperties()
    kotlin("plugin.serialization") version kotlinVersion
    kotlin("js") version kotlinVersion
    val kvisionVersion: String by System.getProperties()
    id("io.kvision") version kvisionVersion
}

version = "1.0.0-SNAPSHOT"
group = "com.example"

repositories {
    mavenCentral()
    jcenter()
    mavenLocal()
}

// Versions
val kotlinVersion: String by System.getProperties()
val kvisionVersion: String by System.getProperties()

val webDir = file("src/main/web")

kotlin {
    js {
        browser {
            runTask {
                outputFileName = "main.bundle.js"
                sourceMaps = false
                devServer = KotlinWebpackConfig.DevServer(
                    open = false,
                    port = 3000,
                    proxy = mutableMapOf(
                        "/kv/*" to "http://localhost:8080",
                        "/kvws/*" to mapOf("target" to "ws://localhost:8080", "ws" to true)
                    ),
                    static = mutableListOf("$buildDir/processedResources/js/main")
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
        binaries.executable()
    }
    sourceSets["main"].dependencies {
        implementation("io.kvision:kvision:$kvisionVersion")
        implementation("io.kvision:kvision-bootstrap:$kvisionVersion")
        implementation("io.kvision:kvision-bootstrap-css:$kvisionVersion")
        implementation("io.kvision:kvision-i18n:$kvisionVersion")
    }
    sourceSets["test"].dependencies {
        implementation(kotlin("test-js"))
        implementation("io.kvision:kvision-testutils:$kvisionVersion")
    }
    sourceSets["main"].resources.srcDir(webDir)
}
```
{% endcode %}

### Source code

The source code for the application is contained in `src/main` directory. It consists of Kotlin sources in `kotlin` directory, optional `resources` \(e.g. images, CSS files, Handlebars templates, translation files\), and main `index.html` file in a `web` directory.

Test sources are contained in `src/test` directory.

### The Application class

The main KVision application class must extend the `io.kvision.Application` class and override the`start` method.

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
    startApplication(::App, module.hot, CoreModule)
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
It's recommended to use Gradle from a command line \(in a terminal window\).
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

You can also debug KVision application directly in IntelliJ IDEA Ultimate by creating new "JavaScript Debug" configuration pointing to [http://localhost:3000](http://localhost:3000) url.

{% hint style="info" %}
Note: Source maps are disabled by default in all KVision template projects, because the development cycle \(hot reload\) is a lot faster without them. To enable source maps you need to remove line `sourceMaps = false` from your `build.gradle.kts` file and remove line `config.devtool = 'eval-cheap-source-map'` from `webpack.config.d/webpack.js` file.
{% endhint %}

