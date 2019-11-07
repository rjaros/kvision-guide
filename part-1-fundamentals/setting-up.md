# Setting up

KVision applications are built with [Gradle](http://gradle.org/). Although it may be possible to manage all the dependencies with other tools \(like [Maven](https://maven.apache.org/)\), the supported way is to use [Kotlin Frontend Plugin](https://github.com/Kotlin/kotlin-frontend-plugin) with Gradle.

KVision applications have dependencies on some Kotlin libraries as well as a few [npm](https://www.npmjs.com/) libraries \(which of course depend on other libraries\). The Kotlin Frontend Plugin provides an easy way to gather dependencies, pack bundles \(via [webpack](https://webpack.github.io/)\) and test the application using [Karma](http://karma-runner.github.io/1.0/index.html). By using Gradle continuous build, you also can get hot module replacement feature \(apply code changes in the browser on the fly\).

## Requirements

To build a typical KVision application you should have some tools installed on your machine and available on the system PATH:

* [JDK](https://jdk.java.net/) 8 or higher
* [NodeJS](https://nodejs.org) with NPM package manager
* [Git](https://git-scm.com) \(with additional UNIX tools if using Windows\)
* GNU [xgettext](https://www.gnu.org/software/gettext) and [msgmerge](https://www.gnu.org/software/gettext) utilities to use [Internationalization](internationalization.md) features    

{% hint style="info" %}
Note: Make sure you are building KVision applications on the local file system.  
{% endhint %}

## Creating a new application

The recommended way to create a new application is to download and copy the [KVision template](https://github.com/rjaros/kvision-examples/tree/master/template) project, available on GitHub.

### build.gradle.kts

The `build.gradle.kts` file is responsible for the definition of the build process. It declares necessary repositories \(e.g. external bintray repositories\) and all required dependencies. It declares `zip` task, typically used to build distribution pack.

{% tabs %}
{% tab title="build.gradle.kts" %}
```kotlin
import org.jetbrains.kotlin.gradle.frontend.KotlinFrontendExtension
import org.jetbrains.kotlin.gradle.frontend.npm.NpmExtension
import org.jetbrains.kotlin.gradle.frontend.webpack.WebPackExtension
import org.jetbrains.kotlin.gradle.targets.js.nodejs.nodeJs

import org.jetbrains.kotlin.gradle.tasks.Kotlin2JsCompile
import org.jetbrains.kotlin.gradle.tasks.KotlinJsDce

buildscript {
    extra.set("production", (findProperty("prod") ?: findProperty("production") ?: "false") == "true")
}

plugins {
    val kotlinVersion: String by System.getProperties()
    id("kotlinx-serialization") version kotlinVersion
    id("kotlin2js") version kotlinVersion
    id("kotlin-dce-js") version kotlinVersion
    kotlin("frontend") version System.getProperty("frontendPluginVersion")
}

version = "1.0.0-SNAPSHOT"
group = "com.example"

repositories {
    jcenter()
    maven { url = uri("https://dl.bintray.com/kotlin/kotlin-eap") }
    maven { url = uri("https://kotlin.bintray.com/kotlinx") }
    maven { url = uri("https://dl.bintray.com/kotlin/kotlin-js-wrappers") }
    maven { url = uri("https://dl.bintray.com/gbaldeck/kotlin") }
    maven { url = uri("https://dl.bintray.com/rjaros/kotlin") }
    mavenLocal()
}

// Versions
val kotlinVersion: String by System.getProperties()
val kvisionVersion: String by project

// Custom Properties
val webDir = file("src/main/web")
val isProductionBuild = project.extra.get("production") as Boolean

dependencies {
    implementation(kotlin("stdlib-js"))
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
    testImplementation(kotlin("test-js"))
    testImplementation("pl.treksoft:kvision-testutils:$kvisionVersion:tests")
}

kotlinFrontend {
    sourceMaps = !isProductionBuild
    npm {
        devDependency("po2json")
        devDependency("grunt")
        devDependency("grunt-pot")
    }
    webpackBundle {
        bundleName = "main"
        sourceMapEnabled = false
        port = 3000
        proxyUrl = "http://localhost:8080"
        contentPath = webDir
        mode = if (isProductionBuild) "production" else "development"
    }

    define("PRODUCTION", isProductionBuild)
}
sourceSets["main"].resources.srcDir(webDir)

tasks {
    withType<Kotlin2JsCompile> {
        kotlinOptions {
            moduleKind = "umd"
            sourceMap = !isProductionBuild
            metaInfo = true
            if (!isProductionBuild) {
                sourceMapEmbedSources = "always"
            }
        }
    }
    withType<KotlinJsDce> {
        dceOptions {
            devMode = !isProductionBuild
        }
        inputs.property("production", isProductionBuild)
        doFirst {
            destinationDir.deleteRecursively()
        }
        doLast {
            copy {
                file("$buildDir/node_modules_imported/").listFiles()?.forEach {
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
        outputs.file("$buildDir/Gruntfile.js")
        doLast {
            file("$buildDir/Gruntfile.js").run {
                writeText(
                    """
                    module.exports = function (grunt) {
                        grunt.initConfig({
                            pot: {
                                options: {
                                    text_domain: "messages",
                                    dest: "../src/main/resources/i18n/",
                                    keywords: ["tr", "ntr:1,2", "gettext", "ngettext:1,2"],
                                    encoding: "UTF-8"
                                },
                                files: {
                                    src: ["../src/main/kotlin/**/*.kt"],
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
        dependsOn("npm-install", "generateGruntfile")
        workingDir = file("$buildDir")
        executable = project.nodeJs.root.nodeCommand
        args("$buildDir/node_modules/grunt/bin/grunt", "pot")
        inputs.files(sourceSets["main"].allSource)
        outputs.file("$projectDir/src/main/resources/i18n/messages.pot")
    }
}
afterEvaluate {
    tasks {
        getByName("processResources", Copy::class) {
            dependsOn("npm-install")
            exclude("**/*.pot")
            doLast("Convert PO to JSON") {
                destinationDir.walkTopDown().filter {
                    it.isFile && it.extension == "po"
                }.forEach {
                    exec {
                        executable = project.nodeJs.root.nodeCommand
                        args(
                            "$buildDir/node_modules/po2json/bin/po2json",
                            it.absolutePath,
                            "${it.parent}/${it.nameWithoutExtension}.json",
                            "-f",
                            "jed1.x"
                        )
                        println("Converted ${it.name} to ${it.nameWithoutExtension}.json")
                    }
                    it.delete()
                }
            }
        }
        getByName("webpack-run").dependsOn("classes")
        getByName("webpack-bundle").dependsOn("classes", "runDceKotlinJs")
        create("webJar", Jar::class) {
            dependsOn("webpack-bundle")
            group = "package"
            val from = project.tasks["webpack-bundle"].outputs.files + webDir
            from(from)
            inputs.files(from)
            outputs.file(archiveFile)

            manifest {
                attributes(
                    mapOf(
                        "Implementation-Title" to rootProject.name,
                        "Implementation-Group" to rootProject.group,
                        "Implementation-Version" to rootProject.version,
                        "Timestamp" to System.currentTimeMillis()
                    )
                )
            }
        }
        create("zip", Zip::class) {
            dependsOn("webpack-bundle")
            group = "package"
            destinationDirectory.set(file("$buildDir/libs"))
            val from = project.tasks["webpack-bundle"].outputs.files + webDir
            from(from)
            inputs.files(from)
            outputs.file(archiveFile)
        }
    }
}

fun KotlinFrontendExtension.webpackBundle(block: WebPackExtension.() -> Unit) =
    bundle("webpack", delegateClosureOf(block))

fun KotlinFrontendExtension.npm(block: NpmExtension.() -> Unit) = configure(block)
```
{% endtab %}
{% endtabs %}

### Source code

The source code for the application is contained in `src/main` directory. It consists of Kotlin sources in `kotlin` directory, optional `resources` \(e.g. images, CSS files, Handlebars templates, translation files\), and main `index.html` file in a `web` directory.

Test sources are contained in `src/test` directory.

### The Application class

The main KVision application class is must extend the `pl.treksoft.kvision.Application` class and override the`start` method.

{% tabs %}
{% tab title="App.kt" %}
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
{% endtab %}
{% endtabs %}

## Development

To run the application with Gradle continuous build, enter:

```text
./gradlew -t run                                    (on Linux)
gradlew.bat -t run                                  (on Windows)
```

After Gradle finishes downloading dependencies and building the application, open [http://localhost:3000/](http://localhost:3000/) in your favorite browser.

You can import the project in **IntelliJ IDEA** and open `src/main/kotlin/com/example/App.kt` file. You can of course use your favorite text editor.

Add some code inside the `start` function:

{% tabs %}
{% tab title="App.kt" %}
```kotlin
override fun start() {
    // ...
    root("kvapp") {
        span("Hello world!")
    }
 }
```
{% endtab %}
{% endtabs %}

You should see your changes immediately in the browser.

## Production

To build a complete application optimized for production, run:

```text
./gradlew -Pprod=true clean zip                   (on Linux)
gradlew.bat -Pprod=true clean zip                 (on Windows)
```

The package containing all of application files will be saved as `build/libs/template-1.0.0-SNAPSHOT.zip`.

