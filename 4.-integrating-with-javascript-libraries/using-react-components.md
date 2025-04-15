# Integrating With React Components

[React](https://reactjs.org/) is one of the most popular JavaScript libraries for building user interfaces. It's architecture is based on encapsulated components that manage their own state. There are a lot of free React components available, you can find a list of most popular on [this page](https://github.com/brillout/awesome-react-components).&#x20;

Although KVision offers a rich set of different components, there will be occasions when you will need to use something that KVision does not offer. Fortunately both KVision and Kotlin/JS ecosystem allows you to include any [NPM](https://www.npmjs.com/) dependencies in your project. And KVision has full support for embedding external React components inside your application. React components are standardized, so it's fairly easy to learn how to use them.

{% hint style="info" %}
Note: KVision support for React components is based on [Kotlin Wrappers](https://github.com/JetBrains/kotlin-wrappers) from JetBrains, so it's good to know how to use these libraries. You can learn the most important things from [this tutorial](https://play.kotlinlang.org/hands-on/Building%20Web%20Applications%20with%20React%20and%20Kotlin%20JS/01_Introduction).&#x20;
{% endhint %}

## Dependencies

To use React component just add `kvision-react` module and the required NPM dependencies to your `build.gradle.kts` file.

```kotlin
kotlin {
// ...
    sourceSets["jsMain"].dependencies {
    // ...
        implementation(npm("react-awesome-button", "6.5.1"))
        implementation(npm("prop-types", "*")) // required by react-awesome-button

        implementation(npm("react-ace", "10.1.0"))
        implementation(npm("ace-builds", "1.12.5"))
        implementation(npm("file-loader", "*")) // required by ace-builds
        
        implementation("io.kvision:kvision:$kvisionVersion")
        implementation("io.kvision:kvision-react:$kvisionVersion")
        // ... other KVision modules
    }
}

```

## Simple components

Let's start with a simple example and [react-awesome-button](https://www.npmjs.com/package/react-awesome-button) component. First you need to declare the type of data being passed into the component.

```kotlin
import react.ComponentType
import react.PropsWithChildren

external interface ReactButtonProps : PropsWithChildren {
    var type: String
    var size: String
    var action: (dynamic, () -> Unit) -> Unit
}

@JsModule("react-awesome-button")
external val reactButtonModule: dynamic

val ReactButton: ComponentType<ReactButtonProps> = reactButtonModule.AwesomeButtonProgress
```

&#x20;Having this declaration, you can use the component with the  `react { ... }` DSL builder function.

```kotlin
react {
    ReactButton {
        type = "primary"
        size = "large"
        action = { _, next ->
            window.setTimeout({
                next()
            }, 3000)
        }
        +"React progress button"
    }
}
```

## Advanced components

React components can be stateful and can maintain internal state data. With KVision it's possible to logically relocate this internal state from React component into KVision component, where it can be accessed from the other parts of the application.

Let's use an advanced ACE code editor with [react-ace](https://www.npmjs.com/package/react-ace) component. The basic declaration is similar to the previous example (of course the component has a lot more properties then covered by this example).

```kotlin
import react.ComponentType
import react.PropsWithChildren

external interface ReactAceProps : PropsWithRef<dynamic>, PropsWithChildren {
    var value: String
    var mode: String
    var theme: String
    var onChange: (String) -> Unit
}

@JsModule("react-ace")
external val reactAceModule: dynamic

@JsModule("ace-builds/webpack-resolver")
external val webpackResolver: dynamic

val AceEditor: ComponentType<ReactAceProps> = reactAceModule.default

class App : Application() {
    init {
        useModule(webpackResolver) // required since webpack 5
    }
    // ...
}
```

Now you can use the component with advanced form of the DSL builder function`react(initialState) { getState, changeState -> ... }`. &#x20;

```kotlin

val ace = react("some initial code") { getState, changeState ->
    AceEditor {
        value = getState()
        mode = "kotlin"
        theme = "monokai"
        onChange = { value -> changeState { value } }
    }
}
button("Get the code").onClick {
    console.log(ace.state)
}
button("Set the code").onClick {
    ace.state = "some new code"
}
```

You initialize the KVision `React` component with some initial state, which can be any type `T` you need. You use `getState(): () -> T` function to retrieve the current state and assign the correct value to the React component input property. And you can use `changeState(): ((T) -> T) -> Unit` function to modify the state (most of the time this function will be used with some React callbacks). After creating this two-way bindings you can both read and change the current state of the component with its `.state` property.

## Accessing internal API of React components

Sometimes it may be necessary to access the internal api of a React component, beyond the attributes exposed in the interface. To do this, you can use the `ref` variable provided by `PropsWithRef<T>`, which is part of the `kotlin-react` library:

```kotlin
    var internalEditor: dynamic = null

    val ace: React<String>

    init {
        ace = react(state) { getState, changeState ->
            val onContainerCallback = useRefCallback<dynamic> { comp ->
                internalEditor = comp?.editor
            }
            AceEditor {
                value = getState()
                // other parameters
                ref = onContainerCallback
            }
        }
    }
    
    fun moveCursorTo(line: Int, col: Int) {
        internalEditor?.moveCursorTo(line, col)
    }
```

## Using KVision components as React children

Most React components can have children. Typically you can easily add other React components as React children. But you may also use KVision components with a help of `kv` helper function.&#x20;

```kotlin
    import io.kvision.react.kv
    
    root("kvapp") {
        react {
            ElTabs {
                ElTabsPane {
                    label = "Tab"
                    kv {
                        textInput(value = "KVision text input inside React")
                    }
                }
            }
        }
    }
```

## Resources

When using React components you will also need to include resources (like CSS). Use `useModule` function inside some `init {}` block for this purpose.

```kotlin
@JsModule("react-awesome-button/dist/themes/theme-blue.css")
external val cssThemeBlue: dynamic

init {
    useModule(cssThemeBlue)
}
```
