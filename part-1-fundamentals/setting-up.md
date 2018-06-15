# Setting up

KVision applications are built with [Gradle](http://gradle.org/). Although it may be possible to manage all the dependencies with other tools \(like [Maven](https://maven.apache.org/)\), the supported way is to use [Kotlin Frontend Plugin](https://github.com/Kotlin/kotlin-frontend-plugin) with Gradle. 

KVision applications have dependencies on some Kotlin libraries as well as a few [npm](https://www.npmjs.com/) libraries \(which of course depend on other libraries\). The Kotlin Frontend Plugin provides an easy way to gather dependencies, pack bundles \(via [webpack](https://webpack.github.io/)\) and test the application using [Karma](http://karma-runner.github.io/1.0/index.html). By using Gradle continuous build, you also can get hot module replacement feature \(apply code changes in the browser on the fly\).

## Creating a new application

To create a new application it is recommended to download and copy [KVision template](https://github.com/rjaros/kvision-examples/tree/master/template) project, available on GitHub.

### build.gradle

The build.gradle file is responsible for the definition of the build process. It declares necessary repositories \(e.g. external bintray repositories\) and required dependencies \(both Kotlin and npm\). It declares `dist` and `distZip` tasks, used to build distribution packs.

{% code-tabs %}
{% code-tabs-item title="build.gradle" %}
```groovy
buildscript {
    ext.production = (findProperty('prod') ?: 'false') == 'true'
    ext.npmdeps = new File("npm.dependencies").getText()

    repositories {
        jcenter()
        maven { url = 'https://dl.bintray.com/kotlin/kotlin-eap' }
        maven { url = 'https://plugins.gradle.org/m2/' }
        maven { url = 'https://kotlin.bintray.com/kotlinx' }
    }

    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:${kotlinVersion}"
        classpath "org.jetbrains.kotlin:kotlin-frontend-plugin:${frontendPluginVersion}"
        classpath "org.jetbrains.kotlinx:kotlinx-gradle-serialization-plugin:${serializationVersion}"
    }
}

apply plugin: 'kotlin2js'
if (production) {
    apply plugin: 'kotlin-dce-js'
}
apply plugin: 'org.jetbrains.kotlin.frontend'
apply plugin: 'kotlinx-serialization'

repositories {
    jcenter()
    maven { url = 'https://kotlin.bintray.com/kotlinx' }
    maven { url = 'https://dl.bintray.com/gbaldeck/kotlin' }
    maven { url = 'https://dl.bintray.com/rjaros/kotlin' }
}

dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-js:${kotlinVersion}"
    compile "pl.treksoft:kvision:${kvisionVersion}"
    compile "org.jetbrains.kotlin:kotlin-test-js:${kotlinVersion}"
}

kotlinFrontend {
    npm {
        dependency("css-loader")
        dependency("style-loader")
        dependency("less")
        dependency("less-loader")
        dependency("imports-loader")
        dependency("uglifyjs-webpack-plugin")
        dependency("file-loader")
        dependency("url-loader")
        dependency("jquery", "3.2.1")
        dependency("fecha", "2.3.2")
        dependency("snabbdom", "0.7.1")
        dependency("snabbdom-virtualize", "0.7.0")
        dependency("navigo", "7.0.0")
        npmdeps.eachLine { line ->
            def (name, version) = line.tokenize(" ")
            dependency(name, version)
        }
        devDependency("karma")
        devDependency("qunit")
    }

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

task copyResources(type: Copy) {
    from "src/main/resources"
    into file(buildDir.path + "/js")
}

task copyResourcesForDce(type: Copy) {
    from "src/main/resources"
    from("${buildDir.path}/node_modules_imported/kvision") {
        include "css/**"
        include "img/**"
        include "js/**"
        include "hbs/**"
    }
    into file(buildDir.path + "/kotlin-js-min/main")
}

task dist(type: Copy, dependsOn: 'bundle') {
    from "src/main/web"
    from "${buildDir.path}/bundle"
    into file(buildDir.path + "/distributions/" + project.name)
}

task distZip(type: Zip, dependsOn: 'dist') {
    from (buildDir.path + "/distributions/" + project.name)
}

afterEvaluate {
    tasks.getByName("webpack-bundle") { dependsOn(copyResources, copyResourcesForDce) }
    tasks.getByName("webpack-run") { dependsOn(copyResources) }
    tasks.getByName("karma-start") { dependsOn(copyResources) }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### npm.dependencies

The npm.dependencies file contains optional npm libraries, which are not required for every application. These can be removed, when components based on their functionality are not used. You can create fully functional KVision application even without any of them \(see TodoMVC example\). By removing these dependencies the size of the distributed application will be smaller. 

{% code-tabs %}
{% code-tabs-item title="npm.dependencies" %}
```text
bootstrap 3.3.7                                     // Bootstrap CSS layout
bootstrap-webpack 0.0.6                             // Bootstrap CSS layout
bootstrap-select 1.12.4                             // for Select / SelectInput component
ajax-bootstrap-select 1.4.3                         // for Select / SelectInput component
bootstrap-datetime-picker 2.4.4                     // for DateTime / DateTimeInput component
bootstrap-touchspin 3.1.1                           // for Spinner / SpinnerInput component
font-awesome 4.7.0                                  // for Font Awesome icons 
font-awesome-webpack github:jarecsni/font-awesome-webpack // for Font Awesome icons
jquery-resizable-dom 0.28.0                         // for SplitPanel component
awesome-bootstrap-checkbox 0.3.7                    // for Checkbox / Radio / RadioGroup components
trix 0.11.1                                         // for RichText component
element-resize-event 2.0.9                          // for Window component
bootstrap-fileinput 4.4.7                           // for Upload / UploadInput component
handlebars 4.0.11                                   // for Handlebar.js templates
handlebars-loader 1.7.0                             // for Handlebar.js templates

```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Source code

The source code for the application is contained in `src/main` directory. It consists of Kotlin sources in `kotlin` directory, optional `resources` \(e.g. images, CSS files, Handlebars templates\), and main `index.html` file in a `web` directory.

Test sources are contained in `src/test` directory.

## Development

To run the application with Gradle continuous build enter:

```text
./gradlew -t run                                    (on Linux)
gradlew.bat -t run                                  (on Windows)
```

After Gradle finishes downloading dependencies and building the application open [http://localhost:8088/](http://localhost:8088/) in your favorite browser.

You can import the project in **IntelliJ IDEA** and open `src/main/kotlin/com/example/App.kt` file. You can of course use your favorite text editor.

Add some code inside the **`start`** function:

{% code-tabs %}
{% code-tabs-item title="App.kt" %}
```kotlin
override fun start(state: Map<String, Any>) {
     Root("kvapp") {
         label("Hello world!")
     }
 }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You should see your changes immediately in the browser.

## Production

To build complete application optimized for production run:

```text
./gradlew -Pprod=true clean distZip                   (on Linux)
gradlew.bat -Pprod=true clean distZip                 (on Windows)
```

The application files will be saved in `build/distributions/template` directory and a package containing all of them will be saved as `build/distributions/template.zip`as well.



