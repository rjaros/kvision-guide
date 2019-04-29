# Setting up

KVision applications are built with [Gradle](http://gradle.org/). Although it may be possible to manage all the dependencies with other tools \(like [Maven](https://maven.apache.org/)\), the supported way is to use [Kotlin Frontend Plugin](https://github.com/Kotlin/kotlin-frontend-plugin) with Gradle.

KVision applications have dependencies on some Kotlin libraries as well as a few [npm](https://www.npmjs.com/) libraries \(which of course depend on other libraries\). The Kotlin Frontend Plugin provides an easy way to gather dependencies, pack bundles \(via [webpack](https://webpack.github.io/)\) and test the application using [Karma](http://karma-runner.github.io/1.0/index.html). By using Gradle continuous build, you also can get hot module replacement feature \(apply code changes in the browser on the fly\).

## Requirements

To build a typical KVision application you should have some tools installed on your machine and available on the system PATH:

* [JDK](https://jdk.java.net/) 8 or higher
* [NodeJS](https://nodejs.org) with NPM package manager
* [Git](https://git-scm.com) \(with additional UNIX tools if using Windows\)
* GNU [xgettext](https://www.gnu.org/software/gettext) and [msgmerge](https://www.gnu.org/software/gettext) utilities to use [Internationalization](internationalization.md) features    

## Creating a new application

The recommended way to create a new application is to download and copy the [KVision template](https://github.com/rjaros/kvision-examples/tree/master/template) project, available on GitHub.

### build.gradle

The build.gradle file is responsible for the definition of the build process. It declares necessary repositories \(e.g. external bintray repositories\) and required dependencies. It declares `dist` and `distZip` tasks, used to build distribution packs.

{% code-tabs %}
{% code-tabs-item title="build.gradle" %}
```groovy
buildscript {
    ext.production = (findProperty('prod') ?: 'false') == 'true'

    repositories {
        jcenter()
        maven { url = 'https://dl.bintray.com/kotlin/kotlin-eap' }
        maven { url = 'https://plugins.gradle.org/m2/' }
        maven { url = 'https://kotlin.bintray.com/kotlinx' }
    }

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:${kotlinVersion}"
        classpath "org.jetbrains.kotlin:kotlin-serialization:${kotlinVersion}"
        classpath "org.jetbrains.kotlin:kotlin-frontend-plugin:${frontendPluginVersion}"
    }
}

plugins {
    id "com.moowork.grunt" version "1.2.0"
}

apply plugin: 'kotlin2js'

if (production) {
    apply plugin: 'kotlin-dce-js'
}
apply plugin: 'org.jetbrains.kotlin.frontend'
apply plugin: 'kotlinx-serialization'

repositories {
    jcenter()
    maven { url = 'https://dl.bintray.com/kotlin/kotlin-eap' }
    maven { url = 'https://kotlin.bintray.com/kotlinx' }
    maven { url = 'https://dl.bintray.com/gbaldeck/kotlin' }
    maven { url = 'https://dl.bintray.com/rjaros/kotlin' }
    mavenLocal()
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-js:${kotlinVersion}"
    compile "pl.treksoft:kvision:${kvisionVersion}"
    compile "pl.treksoft:kvision-bootstrap:${kvisionVersion}"
    compile "pl.treksoft:kvision-select:${kvisionVersion}"
    compile "pl.treksoft:kvision-datetime:${kvisionVersion}"
    compile "pl.treksoft:kvision-spinner:${kvisionVersion}"
    compile "pl.treksoft:kvision-richtext:${kvisionVersion}"
    compile "pl.treksoft:kvision-upload:${kvisionVersion}"
    compile "pl.treksoft:kvision-handlebars:${kvisionVersion}"
    compile "pl.treksoft:kvision-chart:${kvisionVersion}"
    compile "pl.treksoft:kvision-i18n:${kvisionVersion}"
    compile "org.jetbrains.kotlin:kotlin-test-js:${kotlinVersion}"
}

kotlinFrontend {

    webpackBundle {
        bundleName = "main"
        contentPath = file('src/main/web')
        mode = production ? "production" : "development"
    }

    define "PRODUCTION", production

}

compileKotlin2Js {
    kotlinOptions.metaInfo = true
    kotlinOptions.outputFile = "$project.buildDir.path/js/${project.name}.js"
    kotlinOptions.sourceMap = !production
    kotlinOptions.moduleKind = 'umd'
}

compileTestKotlin2Js {
    kotlinOptions.metaInfo = true
    kotlinOptions.outputFile = "$project.buildDir.path/js-tests/${project.name}-tests.js"
    kotlinOptions.sourceMap = !production
    kotlinOptions.moduleKind = 'umd'
}

task pot(type: GruntTask) {
    args = ["pot"]
}

task po2json(type: GruntTask) {
    args = ["default"]
    inputs.dir(file('translation'))
    outputs.dir(file('build/js'))
    outputs.dir(file('build/kotlin-js-min/main'))
}

pot.dependsOn 'installGrunt'
pot.dependsOn 'npmInstall'
po2json.dependsOn 'installGrunt'
po2json.dependsOn 'npmInstall'

task copyResources(type: Copy) {
    from "src/main/resources"
    into file(buildDir.path + "/js")
}

task copyResourcesForDce() {
    doLast {
        copy {
            from "src/main/resources"
            ext.modulesDir = new File("${buildDir.path}/node_modules_imported/")
            modulesDir.eachDir {
                if (it.name.startsWith("kvision")) {
                    from(it) {
                        include "css/**"
                        include "img/**"
                        include "js/**"
                    }
                }
            }
            into file(buildDir.path + "/kotlin-js-min/main")
        }
    }
}

task dist(type: Copy, dependsOn: 'bundle') {
    from "src/main/web"
    from "${buildDir.path}/bundle"
    into file(buildDir.path + "/distributions/" + project.name)
}

task distZip(type: Zip, dependsOn: 'dist') {
    from(buildDir.path + "/distributions/" + project.name)
}

afterEvaluate {
    if (production) {
        tasks.getByName("copyResourcesForDce") { dependsOn(runDceKotlinJs) }
    }
    tasks.getByName("webpack-bundle") {
        dependsOn(po2json, copyResources, copyResourcesForDce)
    }
    tasks.getByName("webpack-run") { dependsOn(po2json, copyResources) }
    tasks.getByName("karma-start") { dependsOn(po2json, copyResources) }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Node.js dependency

The build process uses some Gradle plugins which require Node.js to be installed and available in the system. All npm dependencies are downloaded and managed automatically by Gradle.

### Source code

The source code for the application is contained in `src/main` directory. It consists of Kotlin sources in `kotlin` directory, optional `resources` \(e.g. images, CSS files, Handlebars templates\), and main `index.html` file in a `web` directory.

Test sources are contained in `src/test` directory.

## Development

To run the application with Gradle continuous build, enter:

```text
./gradlew -t run                                    (on Linux)
gradlew.bat -t run                                  (on Windows)
```

After Gradle finishes downloading dependencies and building the application, open [http://localhost:8088/](http://localhost:8088/) in your favorite browser.

You can import the project in **IntelliJ IDEA** and open `src/main/kotlin/com/example/App.kt` file. You can of course use your favorite text editor.

Add some code inside the **`start`** function:

{% code-tabs %}
{% code-tabs-item title="App.kt" %}
```kotlin
override fun start(state: Map<String, Any>) {
    // ...
    root = Root("kvapp") {
        span("Hello world!")
    }
 }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You should see your changes immediately in the browser.

## Production

To build a complete application optimized for production, run:

```text
./gradlew -Pprod=true clean distZip                   (on Linux)
gradlew.bat -Pprod=true clean distZip                 (on Windows)
```

The application files will be saved in `build/distributions/template` directory and a package containing all of them will be saved as `build/distributions/template.zip`as well.
