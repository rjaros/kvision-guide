# Charts

KVision Chart component is based on awesome [Chart.js](https://www.chartjs.org/) library. It allows you to create many different types of charts with a lot of options and additional functionalities. This component is contained in kvision-chart module. KVision adds Kotlin type-safe bindings for most of Chart.js API but you should get familiar with [Chart.js documentation](https://www.chartjs.org/docs/latest/) to achieve best results.

To create a chart use `pl.treksoft.kvision.chart.Chart` class. Its constructor takes one required parameter of `pl.treksoft.kvision.chart.Configuration` class. The configuration object specifies both the data and the options for the chart.

```kotlin
Chart(
    Configuration(
        ChartType.BAR,
        listOf(DataSets(data = listOf(6, 12, 19, 13, 7))), 
        listOf("Africa", "Asia", "Europe", "Latin America", "North America")
    )
)
```

By default the chart component is responsive - its size is calculated based on the size of the outer container. You can set `responsive` option to `false` and use additional constructor parameters, `chartWidth` and `chartHeight` , to create the chart with a fixed size.

```kotlin
Chart(
    Configuration(
        ChartType.BAR,
        listOf(DataSets(data = listOf(6, 12, 19, 13, 7))),
        listOf("Africa", "Asia", "Europe", "Latin America", "North America"),
        Options(responsive = false)
    ), 200, 200
)
```

When specifying colors \(for lines, points, backgrounds, legend, title, hovers etc.\) use KVision's `Color` class.

```kotlin
Chart(
    Configuration(
        ChartType.POLARAREA,
        listOf(
            DataSets(
                data = listOf(6, 12, 19, 13, 7),
                backgroundColor = listOf(
                    Color(0x3e95cd),
                    Color(0x8e5ea2),
                    Color(0x3cba9f),
                    Color(0xe8c3b9),
                    Color(0xc45850)
                )
            )
        ), listOf("Africa", "Asia", "Europe", "Latin America", "North America")
        )
    )
)
```

Internationalization of chart textual data is also fully supported \(including dynamic language change\).

```kotlin
Chart(
    Configuration(
        ChartType.BAR,
        listOf(
            DataSets(
                data = listOf(6, 12, 19, 13, 7)
            )
        ), listOf(
            tr("Africa"),
            tr("Asia"),
            tr("Europe"),
            tr("Latin America"),
            tr("North America")
        )
    )
)
```

You can find more examples of charts usage in the [Showcase](https://rjaros.github.io/kvision-examples/showcase/#!/charts) app in the [kvision-examples](https://github.com/rjaros/kvision-examples) repository on GitHub.

