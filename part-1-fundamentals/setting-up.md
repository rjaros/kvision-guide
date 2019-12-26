# Setting up

KVision applications are built with [Gradle](http://gradle.org/). The official Kotlin/JS gradle plugin is used to manage NPM dependencies, pack bundles \(via [webpack](https://webpack.github.io/)\) and test the application using [Karma](http://karma-runner.github.io/1.0/index.html). By using Gradle continuous build, you also can get hot module replacement feature \(apply code changes in the browser on the fly\).

## Requirements

To build a typical KVision application you should have some tools installed on your machine and available on the system PATH:

* [JDK](https://jdk.java.net/) 8 or higher
* [NodeJS](https://nodejs.org) \(it will be automatically downloaded by Gradle if not available\)
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
import org.jetbrains.kotlin.gradle.tasks.KotlinJsDce

buildscript {
    extra.set("production", (findProperty("prod") ?: findProperty("production") ?: "false") == "true")
}

plugins {
    val kotlinVersion: String by System.getProperties()
    id("kotlinx-serialization") version kotlinVersion
    id("org.jetbrains.kotlin.js") version kotlinVersion
    id("kotlin-dce-js") version kotlinVersion
}

version = "1.0.0-SNAPSHOT"
group = "com.example"

repositories {
    mavenCentral()
    jcenter()
    maven { url = uri("https://dl.bintray.com/kotlin/kotlin-eap") }
    maven { url = uri("https://kotlin.bintray.com/kotlinx") }
    maven { url = uri("https://dl.bintray.com/kotlin/kotlin-js-wrappers") }
    maven {
        url = uri("https://dl.bintray.com/gbaldeck/kotlin")
        metadataSources {
            mavenPom()
            artifact()
        }
    }
    maven { url = uri("https://dl.bintray.com/rjaros/kotlin") }
    mavenLocal()
}

// Versions
val kotlinVersion: String by System.getProperties()
val kvisionVersion: String by System.getProperties()

// Custom Properties
val webDir = file("src/main/web")
val isProductionBuild = project.extra.get("production") as Boolean

kotlin {
    target {
        compilations.all {
            kotlinOptions {
                moduleKind = "umd"
                sourceMap = !isProductionBuild
                if (!isProductionBuild) {
                    sourceMapEmbedSources = "always"
                }
            }
        }
        browser {
            runTask {
                outputFileName = "main.bundle.js"
                devServer = KotlinWebpackConfig.DevServer(
                    open = false,
                    port = 3000,
                    proxy = mapOf("/kv/*" to "http://localhost:8080", "/kvws/*" to "http://localhost:8080"),
                    contentBase = listOf("$buildDir/processedResources/Js/main")
                )
            }
            webpackTask {
                val runDceKotlin by tasks.getting(KotlinJsDce::class)
                dependsOn(runDceKotlin)
            }
            testTask {
                useKarma {
                    useChromeHeadless()
                }
            }
        }
    }
    sourceSets["main"].dependencies {
        implementation(kotlin("stdlib-js"))
        implementation(npm("po2json"))
        implementation(npm("grunt"))
        implementation(npm("grunt-pot"))

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
    }
    sourceSets["test"].dependencies {
        implementation(kotlin("test-js"))
        implementation("pl.treksoft:kvision-testutils:$kvisionVersion:tests")
    }
    sourceSets["main"].resources.srcDir(webDir)
}

tasks {
    withType<KotlinJsDce> {
        dceOptions {
            devMode = !isProductionBuild
        }
        inputs.property("production", isProductionBuild)
        doFirst {
            classpath = classpath.filter { it.extension != "js" }
            destinationDir.deleteRecursively()
        }
        doLast {
            copy {
                file("$buildDir/tmp/expandedArchives/").listFiles()?.forEach {
                    if (it.isDirectory && it.name.startsWith("kvision")) {
                        from(it) {
                            include("css/**")
                            include("img/**")
                            include("js/**")
                        }
                    }
                }
                into(file(buildDir.path + "/kotlin-js-min/main"))
            }
        }
    }
    create("generateGruntfile") {
        outputs.file("$buildDir/js/Gruntfile.js")
        doLast {
            file("$buildDir/js/Gruntfile.js").run {
                writeText(
                    """
                    module.exports = function (grunt) {
                        grunt.initConfig({
                            pot: {
                                options: {
                                    text_domain: "messages",
                                    dest: "../../src/main/resources/i18n/",
                                    keywords: ["tr", "ntr:1,2", "gettext", "ngettext:1,2"],
                                    encoding: "UTF-8"
                                },
                                files: {
                                    src: ["../../src/main/kotlin/**/*.kt"],
                                    expand: true,
                                },
                            }
                        });
                        grunt.loadNpmTasks("grunt-pot");
                    };
                """.trimIndent()
                )
            }
        }
    }
    create("generatePotFile", Exec::class) {
        dependsOn("kotlinNpmInstall", "generateGruntfile")
        workingDir = file("$buildDir/js")
        executable = NodeJsRootPlugin.apply(project).nodeCommand
        args("$buildDir/js/node_modules/grunt/bin/grunt", "pot")
        inputs.files(kotlin.sourceSets["main"].kotlin.files)
        outputs.file("$projectDir/src/main/resources/i18n/messages.pot")
    }
}
afterEvaluate {
    tasks {
        getByName("processResources", Copy::class) {
            dependsOn("kotlinNpmInstall")
            exclude("**/*.pot")
            doLast("Convert PO to JSON") {
                destinationDir.walkTopDown().filter {
                    it.isFile && it.extension == "po"
                }.forEach {
                    exec {
                        executable = NodeJsRootPlugin.apply(project).nodeCommand
                        args(
                            "$buildDir/js/node_modules/po2json/bin/po2json",
                            it.absolutePath,
                            "${it.parent}/${it.nameWithoutExtension}.json",
                            "-f",
                            "jed1.x"
                        )
                        println("Converted ${it.name} to ${it.nameWithoutExtension}.json")
                    }
                    it.delete()
                }
                copy {
                    file("$buildDir/tmp/expandedArchives/").listFiles()?.forEach {
                        if (it.isDirectory && it.name.startsWith("kvision")) {
                            val kvmodule = it.name.split("-$kvisionVersion").first()
                            from(it) {
                                include("css/**")
                                include("img/**")
                                include("js/**")
                                into("$kvmodule/$kvisionVersion")
                            }
                        }
                    }
                    into(file(buildDir.path + "/js/packages_imported"))
                }
            }
        }
        getByName("browserWebpack").dependsOn("processResources")
        create("zip", Zip::class) {
            dependsOn("browserWebpack")
            group = "package"
            destinationDirectory.set(file("$buildDir/libs"))
            val distribution = project.tasks.getByName("browserWebpack",KotlinWebpack::class).destinationDirectory
            from(distribution, webDir)
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
    startApplication(::App)
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

## Production

To build a complete application optimized for production, run:

```text
./gradlew -Pprod=true clean zip                   (on Linux)
gradlew.bat -Pprod=true clean zip                 (on Windows)
```

The package containing all of application files will be saved as `build/libs/template-1.0.0-SNAPSHOT.zip`.

