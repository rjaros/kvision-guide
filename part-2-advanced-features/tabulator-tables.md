# Tabulator tables

KVision Tabulator component is based on awesome [Tabulator](http://tabulator.info) library. It allows you to create interactive and reactive tables, with advanced sorting, filtering and editing capabilities. Tabulator component can be integrated with [observable data model](https://github.com/rjaros/kotlin-observable-js) or [Redux](using-redux.md) store and will automatically react to all changes in your data model.  This component is contained in kvision-tabulator module. KVision adds Kotlin type-safe bindings for most of Tabulator API but you should get familiar with [Tabulator documentation](http://tabulator.info/docs/4.2) to achieve best results.

{% hint style="info" %}
Note: At the moment these functionalities are not supported: tree structure, grouping, mutators, column calculations, download. Please fill a [feature request ](https://github.com/rjaros/kvision/issues/new)if you require any of these.
{% endhint %}

## Creating a table

To create a table use `pl.treksoft.kvision.tabulator.Tabulator` class. Although all constructor parameters have default values, you will usually want to specify Tabulator options with `pl.treksoft.kvision.tabulator.Options` object . The table data can be specified in a few different ways - with local Kotlin collection, local JavaScript array or remote AJAX URL. The table component can be made reactive for all types of local data. 

### Local Kotlin list

Single row data should be specified with a`@Serializable` class. You can use DSL builer function or factory `create` function to automatically get the appropriate serializer for your data type.

```kotlin
@Serializable
data class Book(val title: String, val author: String, val year: Int, val rating: Int)

val model = listOf(
    Book("Fairy tales", "Hans Christian Andersen", 1836, 4),
    Book("Don Quijote De La Mancha", "Miguel de Cervantes", 1610, 4),
    Book("Crime and Punishment", "Fyodor Dostoevsky", 1866, 3),
    Book("In Search of Lost Time", "Marcel Proust", 1920, 5)
)

tabulator(
    model, Options(
        layout = Layout.FITCOLUMNS,
        columns = listOf(
            ColumnDefinition("Title", "title"),
            ColumnDefinition("Author", "author"),
            ColumnDefinition("Year", "year"),
            ColumnDefinition("Rating", "rating", formatter = Formatter.STAR)
        )
    )
) {
    height = 400.px
}
```

To make the Tabulator component reactive just can use `ObservableList` for your model.

### Local JavaScript array

You should use `Any` as your Tabulator type parameter and leave both `data` and `dataSerializer` constructor parameters with `null` values. You pass a reference to native JS array with a `data` parameter inside your `Options` object.

```kotlin
val model = JSON.parse<dynamic>(
                "[{\"title\":\"Fairy tales\", \"author\":\"Hans Christian Andersen\", \"year\":1836, \"rating\":4}," +
                "{\"title\":\"Don Quijote De La Mancha\", \"author\":\"Miguel de Cervantes\", \"year\":1610, \"rating\":4}," +
                "{\"title\":\"Crime and Punishment\", \"author\": \"Fyodor Dostoevsky\", \"year\": 1866, \"rating\": 3}," +
                "{\"title\":\"In Search of Lost Time\", \"author\":\"Marcel Proust\", \"year\":1920, \"rating\":5}]"
            )

tabulator<Any>(Options(
        layout = Layout.FITCOLUMNS,
        columns = listOf(
            ColumnDefinition("Title", "title"),
            ColumnDefinition("Author", "author"),
            ColumnDefinition("Year", "year"),
            ColumnDefinition("Rating", "rating", formatter = Formatter.STAR)
        ), data = model
    )
) {
    height = 400.px
}
```

To make the Tabulator component reactive use `reactiveData = true` parameter inside your `Options` object. 

### Remote AJAX URL

Tabulator tables can get the data from the remote endpoint. You can find more information about available options in the [Tabulator documentation](http://tabulator.info/docs/4.2/data#ajax). All these options, including ajax sorting, pagination and filtering can be used with KVision component.

```kotlin
tabulator<Any>(
    Options(
        ajaxURL = "/remote/tableData",
        ajaxConfig = "POST",
        ajaxContentType = "json",
        ajaxFiltering = true,
        ajaxSorting = true,
        pagination = PaginationMode.REMOTE,
        layout = Layout.FITCOLUMNS,
        columns = listOf(
            ColumnDefinition("Title", "title"),
            ColumnDefinition("Author", "author"),
            ColumnDefinition("Year", "year"),
            ColumnDefinition("Rating", "rating", formatter = Formatter.STAR)
        )
    )
) {
    height = 400.px
}
```

## Local filtering

Tabulator component allows you to filter the data with external, fully type-safe Kotlin code. You can set the filtering function with `setFilter` method, and then use `applyFilter` after the condition change \(e.g. after search input value change\).

```kotlin
val model = listOf(
    Book("Fairy tales", "Hans Christian Andersen", 1836, 4),
    Book("Don Quijote De La Mancha", "Miguel de Cervantes", 1610, 4),
    Book("Crime and Punishment", "Fyodor Dostoevsky", 1866, 3),
    Book("In Search of Lost Time", "Marcel Proust", 1920, 5)
)
val search = textInput(TextInputType.SEARCH) {
    placeholder = "Search ..."
}
val tabulator = tabulator(
    model,
    Options(
        layout = Layout.FITCOLUMNS,
        columns = listOf(
            ColumnDefinition("Title", "title"),
            ColumnDefinition("Author", "author"),
            ColumnDefinition("Year", "year"),
            ColumnDefinition("Rating", "rating", formatter = Formatter.STAR)
        )
    )
) {
    height = 400.px
    setFilter { book ->
        search.value?.let { book.author.contains(it) || book.title.contains(it) } ?: true
    }
}

search.setEventListener {
    input = {
        tabulator.applyFilter()
    }
}
```

