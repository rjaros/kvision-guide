# Working with state

KVision gives you sophisticated but also easy to use tools to work with application state. They are designed with the reactive paradigm and based on `io.kvision.state.ObservableState` and `io.kvision.state.MutableState` interfaces. A lot of KVision classes implement one or both of these interfaces:

* `ObservableValue<T>` - a single value observable
* `ObservableList<T>` - an observable list of elements
* `ObservableSet<T>` - an observable set of elements
* `ReduxStore` -  full-featured Redux state container    
*  all form components \(e.g. `Text`, `TextInput`, `Select`, `SelectInput`, ...\)

Every component implementing `ObservableState` interface can notify its subscribers about state changes. You can subscribe manually by using the `subscribe` function:

```kotlin
fun subscribe(observer: (S) -> Unit): () -> Unit
```

or you can use automatic data binding with `bind` function or dedicated DSL builders:

```kotlin
val observable = ObservableValue("test")
div().bind(observable) { state ->
    +state
}
div(observable) { state ->
    +state
}
```

You can bind directly your data producers \(observables\) with data consumers \(observers\):

```kotlin
val text = text(label = "Enter something")
div(text) {
    +"You entered: $it"
}
```

All form components implement `MutableState` interface. At the same time every form component can be bidirectionally bound to the external `MutableState`instance \(e.g. `ObservableValue`\).

```kotlin
val observable = ObservableValue("test")
text().bindTo(observable)
div(observable) { state ->
    +state
}
```

Yo can of course easily bind two form components with each other.

```kotlin
val text1 = text()
val text2 = text()
text1.bindTo(text2)
```

## Flows

With additional extension functions defined in `kvision-event-flow` module, you can also use Kotlin `Flow` and its operators to declare data bindings between different components and data stores.

```kotlin
class App : Application(), CoroutineScope by CoroutineScope(Dispatchers.Default) {

    override fun start() {
        val store = object : ObservableValue<String>("") {
            fun update(v: String?) {
                value = v ?: ""
            }
            fun addDot() {
                value += "."
            }
        }
        root("kvapp", ContainerType.FIXED) {
            div(className = "jumbotron") {
                width = 100.perc
                div(className = "card") {
                    div(className = "card-body") {
                        text(store, label = "Input") {
                            placeholder = "Add some input"
                            value = it
                        }.stateFlow.onEach { store.update(it) }.launchIn(this@App)
                        text(store, label = "Value") {
                            readonly = true
                            value = it
                        }
                        div(className = "form-group") {
                            button("Add a dot").clickFlow.onEach { store.addDot() }.launchIn(this@App)
                        }
                    }
                }
            }
        }
    }
}
```

## Sub-stores

If case of a complex data store, you can easily create sub-stores, using `sub()` extension function. It's a way to partition your data and optimize rendering, because sub-store will notify its observers only when the specified part of the data changes.

```kotlin
data class ComplexData(val number: Int, val text: String, val check: Boolean)

val mainStore = ObservableValue(ComplexData(1, "2", true))
val subStore = mainStore.sub { it.a }

simplePanel(subStore) { number ->
    div("$number")
}
```

 



