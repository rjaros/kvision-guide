# Observable data model

Any non trivial application works with some kind of data, and providing a means for users to view and modify the data is very important task for user interface development. KVision brings you support for observable data model and one or two-way data binding, using the [kotlin-observable-js](https://github.com/rjaros/kotlin-observable-js) library and classes from `pl.treksoft.kvision.data` package.

### Data model

This implementation assumes that your data can be modeled as a kind of a list \(it can be a single-element list, too\). Having regard to the above, you have to keep your data in the instance of the `ObservableList` data structure. Your data items can be both immutable or mutable. If you like your data elements to be immutable just use a data class.

```kotlin
data class Tweet(val date: Date, 
    val nick: String, 
    val message: String)
```

But sometimes it may be easier to use a mutable objects - in such case you should extend`pl.treksoft.kvision.data.BaseDataComponent` and use delegated properties when declaring your class.

```kotlin
class Tweet(date: Date, nick: String, message: String) : BaseDataComponent() {
    var date by obs(date)
    var nick by obs(nick)
    var message by obs(message)
}
```

To create your complete data model just instantiate an `ObservableList`.

```kotlin
import com.lightningkite.kotlin.observable.list.observableListOf

val tweets = observableListOf<Tweet>()
```

Of course you may initialize your list with some data elements.

```kotlin
import com.lightningkite.kotlin.observable.list.observableListOf

val tweets = observableListOf(
    Tweet(Date(), "User1", "How are you?"),
    Tweet(Date(), "User2", "Good! Thx!")
)
```

