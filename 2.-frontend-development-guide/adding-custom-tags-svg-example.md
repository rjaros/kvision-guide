# Adding custom tags (SVG example)

You may need to include HTML tags that aren't provided out of the box. For example, your app may need to draw SVG diagrams.

To support this, kvision provides the **CustomTag** class, illustrated by example as follows:

```kotlin
fun Div.svg(width: Int, height: Int, block: CustomTag.() -> Unit = {}) {
    customTag("svg") {
        setAttribute("width", "$width")
        setAttribute("height", "$height")
        block()
    }
}

fun CustomTag.line(x1: Int, y1: Int, x2: Int, y2: Int, stroke: Col, block: CustomTag.() -> Unit = {}) {
    customTag("line") {
        setAttribute("x1", "$x1")
        setAttribute("y1", "$y1")
        setAttribute("x2", "$x2")
        setAttribute("y2", "$y2")
        setAttribute("stroke", stroke.name)
        block()
    }
}
```

To use it, we can simply do:

```kotlin
       root("kvapp") {
            div {
                svg(500, 500) {
                    line(0, 0, 100, 100, Col.AQUA)
                    line(100, 100, 150, 150, Col.BLACK)
                }
            }
        }
```
