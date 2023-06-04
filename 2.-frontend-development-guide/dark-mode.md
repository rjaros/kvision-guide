# Dark mode

KVision fully supports Bootstrap dark mode. You can use `io.kvision.theme.ThemeManager` component from the `kvision-bootstrap` module to work with color themes.&#x20;

```kotlin
import io.kvision.theme.ThemeManager

class MyApp : Application() {
    init {
        ThemeManager.init()
    }
}
```

By default theme manager is initialized with auto-detected mode and saves the mode selected by the user in the browser local storage. You can modify this behavior with additional parameters of the `init()` method.

```kotlin
import io.kvision.theme.Theme
import io.kvision.theme.ThemeManager

class MyApp : Application() {
    init {
        ThemeManager.init(initialTheme = Theme.DARK, remember = false)
    }
}
```

You can use `ThemeManager.theme` property to manually select the color theme.

```kotlin
button("Switch to dark mode").onClick {
    ThemeManager.theme = Theme.DARK
}
```

You can also use built-in `ThemeSwitcher`, component which is a `Button` subclass designed as the color mode toggler (you need to also include Font Awesome module for icons).

```kotlin
themeSwitcher(style = ButtonStyle.OUTLINESECONDARY, round = true)
```
