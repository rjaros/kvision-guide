# Using Redux

[Redux](https://redux.js.org/) is a popular predictable state container for JavaScript. [ReduxKotlin](https://reduxkotlin.org/) is a multiplatform Kotlin library, created from scratch as a port of the JavaScript Redux. KVision contains the kvision-redux-kotlin module, based on this Kotlin library. You can use the full power of Redux in your KVision applications by adding the kvision-redux-kotlin module to your `build.gradle.kts` file. This module contains a dedicated implementation of `ReduxStore.`

### State

The state of the application is hidden inside the Redux store. It can not be modified from outside. The only way to change the state is to use the actions and the reducer function.

It's recommended to use a data class to represent the state.

```kotlin
data class MyState(val content: String, val counter: Int)
```

### Actions

Actions are used to describe the possible changes of the state. Actions are represented as classes and they can contain additional data.

An action class have to inherit (directly or indirectly) from `io.kvision.redux.RAction`. It's recommended to use a sealed class, to be able to easily use  exhaustive `when` expression.

```kotlin
sealed class MyAction : RAction {
    object Increment : MyAction()
    object Decrement : MyAction()
    data class SetContent(val content: String) : MyAction()
}
```

### Reducer function

The reducer function actually changes the state. It is called after an action is dispatched to the store. A reducer is a pure function, that gets the current state and action, and calculates the new state of the application.&#x20;

```kotlin
fun myReducer(state: MyState, action: MyAction): MyState = when (action) {
    is MyAction.Increment -> {
        state.copy(counter = state.counter + 1)
    }
    is MyAction.Decrement -> {
        state.copy(counter = state.counter - 1)
    }
    is MyAction.SetContent -> {
        state.copy(content = action.content)
    }
}
```

{% hint style="info" %}
Note: You should not mutate the state parameter. It's recommended to use immutable data types.
{% endhint %}

### Store

You create a Redux store with a `createTypedReduxStore` function, passing the reducer function reference and the initial state of the application.

```kotlin
val store = createTypedReduxStore(::myReducer, MyState("Hello World!", 0))
```

You can get the current state with a `getState` method.

```kotlin
val currentState: MyState = store.getState()
```

### Dispatching actions

To change the state of the application you have to use the `dispatch` method of the store object.

```kotlin
store.dispatch(MyAction.Increment)
store.dispatch(MyAction.SetContent("Welcome to KVision!"))
println(store.getState()) // MyState("Welcome to KVision!", 1)
```

You can also dispatch functions, so called "action creators", which get `dispatch` and `getState` functions as parameters. This is the recommended way to dispatch actions asynchronously.&#x20;

```kotlin
store.dispatch { dispatch, getState ->
    window.setTimeout({
        dispatch(MyAction.Increment)
    }, 1000)
}
```

### Subscribing to the store

Any part of your application can be notified about the new state of the Redux store. You can use the `subscribe` method of the store for that purpose.

```kotlin
store.subscribe { state ->
    println(state)
}
```

### State binding

To help you describe the relationship between the state of the Redux store and the appearance of the application, KVision allows you to bind the store state with a content of any container. By using the `bind` extension function, you can use standard KVision DSL builders to easily represent the UI as the function of the state. The container content will be automatically refreshed after the state of the application changes.

```kotlin
hPanel(spacing = 10).bind(store) { state ->
    for (i in 1..state.counter) {
        div(state.content)
    }
}
```

{% hint style="info" %}
Note: You can have multiple containers bound to the same Redux store. You can also create and use multiple stores, although this is not considered a good practice.
{% endhint %}

