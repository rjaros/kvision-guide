# Buttons and Toolbars

## Buttons

KVision can render buttons in one of 17 styles \(e.g. PRIMARY, SECONDARY, SUCCESS, INFO, WARNING, DANGER, LINK, LIGHT, DARK, OUTLINEPRIMARY, OUTLINESECONDARY, OUTLINESUCCESS, OUTLINEINFO, OUTLINEWARNING, OUTLINEDANGER, OUTLINELIGHT, OUTLINEDARK\) and additional two sizes \(LARGE, SMALL\). Buttons can have a label as well as an icon or a custom image. The `io.kvision.html.Button` class offers a shortcut `onClick` method, which gives an easy way to bind a handler to the click event.

```kotlin
vPanel {
    button("Default button", style = ButtonStyle.PRIMARY) {
        onClick {
            println("Default button clicked")
        }
    }
    button("Cancel", "fas fa-times", ButtonStyle.WARNING) {
        size = ButtonSize.LARGE
        onClick {
            App.cancel()
        }
    }
}
```

### Toolbars

You can join some buttons together to create a button group and join some groups to create a toolbar. In KVision you use `io.kvision.toolbar.ButtonGroup` and `io.kvision.toolbar.Toolbar` classes for this purpose. Use DSL builders to easily create even complex toolbars.

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

