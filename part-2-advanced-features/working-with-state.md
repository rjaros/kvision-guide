# Working with state

KVision gives you sophisticated but also easy to use tools to work with application state. They are designed with the reactive paradigm and based on `pl.treksoft.kvision.ObservableState` interface. A lot of KVision classes implement this interface:

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



