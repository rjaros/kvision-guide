# Navigation and menus

## Navbar

{% hint style="info" %}
This component is only available with the `kvision-bootstrap` module.
{% endhint %}

This components allows you to create a navigation bar on the top or bottom of the page. A navigation bar can contain different types of navigation components: links, menus and forms. A navigation bar is created with the `io.kvision.navbar.Navbar` class. You can choose a label, a type (normal, fixed to top, fixed to bottom, sticky to top) and also foreground and background colors.&#x20;

```kotlin
navbar(label = "A super brand", type = NavbarType.FIXEDTOP, nColor = NavbarColor.DARK) {
    // ...
}
```

Next you can add some navs (by using the `io.kvision.navbar.Nav` or `io.kvision.navbar.Navform` classes) and finally you can add your navigation components.

```kotlin
navbar("NavBar") {
    nav {
        navLink("File", icon = "fas fa-file")
        navLink("Edit", icon = "fas fa-bars")
        dropDown(
            "Favourites",
            listOf("HTML" to "#!/basic", "Forms" to "#!/forms"),
            icon = "fas fa-star",
            forNavbar = true
        )
    }
    navForm {
        text(label = "Search:")
        checkBox(label = "Search") {
            inline = true
        }
    }
    nav(rightAlign = true) {
        navLink("System", icon = "fab fa-windows")
    }
}
```

{% hint style="info" %}
Please note setting `forNavbar = true` property when adding the `DropDown` component to the navigation bar changes the look to match an inline dropdown.
{% endhint %}

## DropDown

{% hint style="info" %}
This component is only available with the `kvision-bootstrap` module.
{% endhint %}

This component displays a menu with a list of links. It can be used inside the `Navbar` container or as a standalone component anywhere in your application. The `io.kvision.dropdown.DropDown` class has a bunch of useful options. You can choose button style and size, using all style and size attributes available for standard buttons. You can choose whether you want a caret on the button and in which direction the menu will open.

The content of the menu can be defined in two ways. You can use `elements: List<StringPair>?` parameter of the class constructor and define all menu items from a static list. You can even use special options from the `DD` enum class to make an item a header, a separator or to make it disabled.

```kotlin
dropDown(
    "Dropdown with special options", elements = listOf(
        "Header" to DD.HEADER.option,
        "Basic formatting" to "#!/basic",
        "Forms" to "#!/forms",
        "Buttons" to "#!/buttons",
        "Separator" to DD.SEPARATOR.option,
        "Dropdowns (disabled)" to DD.DISABLED.option,
        "Separator" to DD.SEPARATOR.option,
        "Containers" to "#!/containers"
    ), "fas fa-asterisk", style = ButtonStyle.PRIMARY
)
```

Another way is to add all children components to the `DropDown` container manually. Use this way to have full control over menu content (e.g. add images or other non-text components). Use `io.kvision.dropdown.Header` and `io.kvision.dropdown.Separator` helper classes to create header or separator items. As always you should use DSL builders whenever possible.

```kotlin
dropDown("Dropdown with custom list", icon = "fas fa-picture-o") {
    minWidth = 250.px
    image(catJpg) { margin = 10.px; title = "Cat" }
    separator()
    image(dogJpg) { margin = 10.px; title = "Dog" }
}
```

## ContextMenu

{% hint style="info" %}
This component is only available with the `kvision-bootstrap` module.
{% endhint %}

This component allows you to define a context menu for any widget. The menu is typically opened with a right mouse button click. The `io.kvision.dropdown.ContextMenu` class is a subclass of a `io.kvision.html.ListTag`. To create a menu, you can add components like `io.kvision.html.Link`, `io.kvision.dropdown.Header`, `io.kvision.dropdown.Separator` or others to the `ContextMenu` container . Next, bind the menu to your widget with the `setContextMenu` method of some `io.kvision.core.Widget` class. You can also use DSL builders to simplify all of this.

```kotlin
contextMenu {
    header("Menu header")
    cmLink("First option", "#!/first")
    cmLink("Second option", "#!/second")
    separator()
    dropDown("Submenu", forDropDown = true) {
        ddLink("Third option", "#!/third")
        ddLink("Fourth option", "#!/fourth")
    }
}
```

{% hint style="info" %}
Please note setting `forDropDown = true` property when adding the `DropDown` component to the the context menu.
{% endhint %}
