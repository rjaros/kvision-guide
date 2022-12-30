# Toasts

The `kvision-toastify` module contains ready to use functions to display toast messages - small, non-disruptive notification windows, which disappear automatically. To display a toast message just call one of the `primary`, `secondary`, `success`, `info`, `warning`, `danger` , `light` and `dark` functions of the `Toast` object.&#x20;

```kotlin
Toast.info("This is a toast message")

Toast.danger("There was an error!")
```

You can also pass `ToastOptions` parameter, to configure how the toast message is displayed.

```kotlin
Toast.success(
    "This is a toast message",
    options = ToastOptions(
        position = ToastPosition.TOPLEFT,
        close = true,
        duration = 10000
    )
)
```
