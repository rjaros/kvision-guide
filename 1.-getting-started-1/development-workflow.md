# Development Workflow

To run the application with Gradle continuous build, enter:

```
./gradlew -t run                                    (on Linux)
gradlew.bat -t run                                  (on Windows)
```

After Gradle finishes downloading dependencies and building the application, open [http://localhost:3000/](http://localhost:3000/) in your favorite browser.

{% hint style="info" %}
If you are working with a fullstack project, the tasks names are different. Please check [this chapter](../6.-full-stack-development-guide/setting-up-1.md) for details.
{% endhint %}

You can import the project in **IntelliJ IDEA** and open `src/jsMain/kotlin/com/example/App.kt` file. You can of course use your favorite text editor.

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
It's recommended to use Gradle from a command line (in a terminal window).
{% endhint %}

