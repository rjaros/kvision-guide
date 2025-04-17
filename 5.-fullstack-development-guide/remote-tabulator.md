# Tabulator Remote

The `io.kvision.tabulator.TabulatorRemote` component, contained in the `kvision-tabulator-remote` module, is a subclass of the `Tabulator` component, dedicated for use with the fullstack interfaces. Unlike standard Tabulator component (which can also load data from an AJAX source but needs a defined endpoint) `TabulatorRemote` is bound directly to the method of the remote service. The method signature looks like this:

```kotlin
import dev.kilua.rpc.annotations.RpcService
import dev.kilua.rpc.RemoteData
import dev.kilua.rpc.RemoteFilter
import dev.kilua.rpc.RemoteSorter

@Serializable
data class Row(val column1: String, val column2: String, val column3: String)

@RpcService
interface IRowDataService {
    suspend fun rowData(page: Int?, size: Int?, filter: List<RemoteFilter>?, sorter: List<RemoteSorter>?, state: String?): RemoteData<Row>
}
```

This model is prepared for server side pagination, sorting, filtering and also receiving external state, but the parameters are nullable, and will be sent only when configured by the appropriate `TabulatorOptions`.

```kotlin
tabulatorRemote(
    getServiceManager(),
    IRowDataService::rowData,
    { someState.toString() },
    TabulatorOptions(
        layout = Layout.FITCOLUMNS,
        pagination = true,
        paginationMode = PaginationMode.REMOTE,
        paginationSize = 3,
        filterMode = FilterMode.REMOTE,
        sortMode = SortMode.REMOTE,
        columns = listOf(
            ColumnDefinition("Column 1", Row::column1.name, headerFilter = Editor.INPUT),
            ColumnDefinition("Column 2", Row::column2.name),
            ColumnDefinition("Column 3", Row::column3.name)
        )
    ), serializer = serializer()
)
```

The `page` parameter contains the current requested page, the `size` parameter - the number of requested rows, the `filter` and `sorter` parameters contain values of selected filters and sorters. The returned data is always wrapped into `RemoteData` class, defined as:

```kotlin
@Serializable
data class RemoteData<T>(val data: List<T> = listOf(), val last_page: Int = 0)
```

You can ignore `last_page` value, if the `RemoteTabulator` component is not configured for remote pagination.
