# Tabulator tables

KVision Tabulator component is based on awesome [Tabulator](http://tabulator.info) library. It allows you to create interactive and reactive tables, with advanced sorting, filtering and editing capabilities. Tabulator component can be integrated with [observable data model](https://github.com/rjaros/kotlin-observable-js) or [Redux](using-redux.md) store and will automatically react to all changes in your data model. This component is contained in kvision-tabulator module. KVision adds Kotlin type-safe bindings for most of Tabulator API but you should get familiar with [Tabulator documentation](http://tabulator.info/docs/4.2) to achieve best results.

{% hint style="info" %}
Note: At the moment these functionalities are not supported: tree structure, grouping, mutators, column calculations, download. Please fill a [feature request ](https://github.com/rjaros/kvision/issues/new)if you require any of these.
{% endhint %}

## Creating a table

To create a table use `pl.treksoft.kvision.tabulator.Tabulator` class. Although all constructor parameters have default values, you will usually want to specify Tabulator options with `pl.treksoft.kvision.tabulator.TabulatorOptions` object . The table data can be specified in a few different ways - with local Kotlin collection, local JavaScript array or remote AJAX URL. The table component can be made reactive for all types of local data.

### Local Kotlin list

Data classes are preferred objects representing a single row data.

```kotlin
data class Book(val title: String, val author: String, val year: Int, val rating: Int)

val model = listOf(
    Book("Fairy tales", "Hans Christian Andersen", 1836, 4),
    Book("Don Quijote De La Mancha", "Miguel de Cervantes", 1610, 4),
    Book("Crime and Punishment", "Fyodor Dostoevsky", 1866, 3),
    Book("In Search of Lost Time", "Marcel Proust", 1920, 5)
)

tabulator(
    model, options = TabulatorOptions(
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

To make the Tabulator component reactive just can use `ObservableList` for your model. You can also use Redux store as a table data source.

### Local JavaScript array

You should use `Any` as your Tabulator type parameter and leave both `data` and `dataSerializer` constructor parameters with `null` values. You pass a reference to native JS array with a `data` parameter inside your `TabulatorOptions` object.

```kotlin
val model = JSON.parse<dynamic>(
                "[{\"title\":\"Fairy tales\", \"author\":\"Hans Christian Andersen\", \"year\":1836, \"rating\":4}," +
                "{\"title\":\"Don Quijote De La Mancha\", \"author\":\"Miguel de Cervantes\", \"year\":1610, \"rating\":4}," +
                "{\"title\":\"Crime and Punishment\", \"author\": \"Fyodor Dostoevsky\", \"year\": 1866, \"rating\": 3}," +
                "{\"title\":\"In Search of Lost Time\", \"author\":\"Marcel Proust\", \"year\":1920, \"rating\":5}]"
            )

tabulator<Any>(TabulatorOptions(
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

To make the Tabulator component reactive use `reactiveData = true` parameter inside your `TabulatorOptions` object.

### Remote AJAX URL

Tabulator tables can get the data from the remote endpoint. You can find more information about available options in the [Tabulator documentation](http://tabulator.info/docs/4.2/data#ajax). All these options, including ajax sorting, pagination and filtering can be used with KVision component.

```kotlin
tabulator<Any>(
    TabulatorOptions(
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

## Formatting the data

### Built-in formatters

You can use many different formatters to present the data in the tabulator cells. Just pass the desired `Formatter` as a `formatter` parameter in `ColumnDefinition`.

```kotlin
val model = listOf(
    Book("Fairy tales", "Hans Christian Andersen", 1836, 4),
    Book("Don Quijote De La Mancha", "Miguel de Cervantes", 1610, 4),
    Book("Crime and Punishment", "Fyodor Dostoevsky", 1866, 3),
    Book("In Search of Lost Time", "Marcel Proust", 1920, 5)
)
val tabulator = tabulator(
    model, options = TabulatorOptions(
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

Tabulator currently supports the following built-in formatter types: `Formatter.PLAINTEXT`, `Formatter.TEXTAREA`, `Formatter.HTML`, `Formatter.MONEY`, `Formatter.IMAGE`, `Formatter.LINK`, `Formatter.DATETIME`, `Formatter.DATATIMEDIFF`, `Formatter.TICKCROSS`, `Formatter.COLOR`, `Formatter.STAR`, `Formatter.TRAFFIC`, `Formatter.PROGRESS`, `Formatter.LOOKUP`, `Formatter.BUTTONTICK`, `Formatter.BUTTONCROSS`, `Formatter.ROWNUM`, `Formatter.HANDLE`. You can find more information about formatters configuration in the [Tabulator docs](http://tabulator.info/docs/4.2/format). 

{% hint style="info" %}
Note: You need to include kvision-moment module to use built-in date/time formatters.
{% endhint %}

### Custom formatters

You can use any KVision components to display data in Tabulator cells. You define the component with the `formatterComponentFunction` property of the `ColumnDefinition` class. When the Tabulator component is bound to the Kotlin data source, this function gives you also direct and type-safe access to the Kotlin data model for the current row.

```kotlin
data class Employee(
    val name: String?,
    val position: String?,
    val office: String?,
    val active: Boolean = false,
    val startDate: Date?,
    val salary: Int?
)
// ...
val model = mutableListOf<Employee>()
// ...
tabulator(model, options = TabulatorOptions(layout = Layout.FITCOLUMNS,
    columns = listOf(
    // ...
      ColumnDefinition(
        "Start date",
        "startDate", formatterComponentFunction = { _, _, data: Employee ->
            Span(data.startDate?.toStringF("YYYY-MM-DD"))
        }
      )
    )
    // ...
))
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
    model, options = TabulatorOptions(
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

## Editing the table

### Built-in editors

Tabulator also allows you to edit the values in each cell according to a user specified `Editor` type. Just pass the desired `Editor` as an `editor` parameter in `ColumnDefinition`.

```kotlin
val model = listOf(
    Book("Fairy tales", "Hans Christian Andersen", 1836, 4),
    Book("Don Quijote De La Mancha", "Miguel de Cervantes", 1610, 4),
    Book("Crime and Punishment", "Fyodor Dostoevsky", 1866, 3),
    Book("In Search of Lost Time", "Marcel Proust", 1920, 5)
)
val tabulator = tabulator(
    model, options = TabulatorOptions(
        layout = Layout.FITCOLUMNS,
        columns = listOf(
            ColumnDefinition("Title", "title", editor = Editor.INPUT),
            ColumnDefinition("Author", "author", editor = Editor.INPUT),
            ColumnDefinition("Year", "year", editor = Editor.NUMBER),
            ColumnDefinition("Rating", "rating", formatter = Formatter.STAR, editor = Editor.STAR)
        )
    )
) {
    height = 400.px
}
```

Tabulator currently supports the following built-in editor types: `Editor.INPUT`, `Editor.TEXTAREA`, `Editor.NUMBER`, `Editor.RANGE`, `Editor.TICK`, `Editor.STAR`, `Editor.SELECT` and `Editor.AUTOCOMPLETE`

### Custom editors

You can use most of KVision components to edit data in Tabulator cells. You define the component with the `editorComponentFunction` property of the `ColumnDefinition` class. When the Tabulator component is bound to the Kotlin data source, this function gives you also direct and type-safe access to the Kotlin data model for the current row.

```kotlin
data class Employee(
    val name: String?,
    val position: String?,
    val office: String?,
    val active: Boolean = false,
    val startDate: Date?,
    val salary: Int?
)
// ...
val model = mutableListOf<Employee>()
// ...
tabulator(model, options = TabulatorOptions(layout = Layout.FITCOLUMNS,
    columns = listOf(
    // ...
      ColumnDefinition(
        "Start date",
        "startDate", editorComponentFunction = { _, _, success, _, data: Employee ->
            DateTimeInput(value = data.startDate, format = "YYYY-MM-DD").apply {
                size = InputSize.SMALL
                clearBtn = false
                setEventListener<DateTimeInput> {
                    change = {
                        success(self.value?.toStringF())
                    }
                }
            }
        })
    )
    // ...
))
```

{% hint style="info" %}
Note: Not all KVision edit controls work well inside Tabulator cells. Currently you cannot use `Select` component, because of conflict in event handling. You can use `SimpleSelect` without problems, though.
{% endhint %}

### Automatic model update

When using editable table with Tabulator component bound to Kotlin `MutableList` data model, the changed data is automatically updated in the model. You can disable this function by setting `dataUpdateOnEdit` Tabulator constructor parameter to `false`.

 

