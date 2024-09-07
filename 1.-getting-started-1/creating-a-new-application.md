# Creating a New Application

## Creating a new application

The recommended way to create a new application is to use [KVision Project Wizard](https://plugins.jetbrains.com/plugin/16533-kvision-project-wizard) plugin for IntelliJ IDEA. Just install the plugin from JetBrains Marketplace in your IDE and run `File -> New -> Project...` from the main menu. Choose `KVision` group, select desired project type and choose modules from the list on the first page. On the second page choose your project name and location. Your project will be generated and opened in IDE when you click `Finish` button. After a few moments required to download all required dependencies you are ready to go.

You can also download and copy the [KVision template](https://github.com/rjaros/kvision-examples/tree/master/template) project, available on GitHub.

### build.gradle.kts

The `build.gradle.kts` file is responsible for the definition of the build process. It declares all required dependencies (in particular KVision optional modules). KVision Gradle plugin is used to simplify the configuration.&#x20;

{% code title="build.gradle.kts" %}
```kotlin
plugins {
    val kotlinVersion: String by System.getProperties()
    kotlin("plugin.serialization") version kotlinVersion
    kotlin("multiplatform") version kotlinVersion
    val kvisionVersion: String by System.getProperties()
    id("io.kvision") version kvisionVersion
}

version = "1.0.0-SNAPSHOT"
group = "com.example"

repositories {
    mavenCentral()
    mavenLocal()
}

// Versions
val kotlinVersion: String by System.getProperties()
val kvisionVersion: String by System.getProperties()

kotlin {
    js(IR) {
        browser {
            commonWebpackConfig {
                outputFileName = "main.bundle.js"
                sourceMaps = false
            }
            testTask {
                useKarma {
                    useChromeHeadless()
                }
            }
        }
        binaries.executable()
    }
    sourceSets["jsMain"].dependencies {
        implementation("io.kvision:kvision:$kvisionVersion")
        implementation("io.kvision:kvision-bootstrap:$kvisionVersion")
        implementation("io.kvision:kvision-i18n:$kvisionVersion")
    }
    sourceSets["jsTest"].dependencies {
        implementation(kotlin("test-js"))
        implementation("io.kvision:kvision-testutils:$kvisionVersion")
    }
}
```
{% endcode %}

### Source code

The source code for the application is contained in `src/jsMain` directory. It consists of Kotlin sources in `kotlin` directory, optional `resources` (e.g. images, CSS files, Handlebars templates, translation files), and main `index.html` file in a `web` directory.

Test sources are contained in `src/jsTest` directory.

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

##
