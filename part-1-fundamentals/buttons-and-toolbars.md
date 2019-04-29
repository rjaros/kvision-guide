# Buttons and toolbars

## Buttons

KVision can render buttons in one of seven styles \(DEFAULT, PRIMARY, SUCCESS, INFO, WARNING, DANGER, LINK\) and one of three sizes \(LARGE, SMALL, XSMALL\). Buttons can have a label as well as an icon \(Glyphicon or Font Awesome\) or a custom image. The `pl.treksoft.kvision.html.Button` class offers a shortcut `onClick` method, which gives an easy way to bind a handler to the click event.

```kotlin
vPanel {
    button("Default button", style = ButtonStyle.DEFAULT) {
        onClick {
            println("Default button clicked")
        }
    }
    button("Cancel", "fa-times", ButtonStyle.WARNING) {
        size = ButtonSize.LARGE
        onClick {
            App.cancel()
        }
    }
}
```

### Toolbars

You can join some buttons together to create a button group and join some groups to create a toolbar. In KVision you use `pl.treksoft.kvision.toolbar.ButtonGroup` and  `pl.treksoft.kvision.toolbar.Toolbar` classes for this purpose. Use DSL builders to easily create even complex toolbars.

```kotlin
toolbar {
    buttonGroup {
        button("<<")
    }
    buttonGroup {
        button("1", disabled = true)
        button("2")
        button("3")
    }
    buttonGroup {
        span("...")
    }
    buttonGroup {
        button("10")
    }
    buttonGroup {
        button(">>")
    }
}
```
