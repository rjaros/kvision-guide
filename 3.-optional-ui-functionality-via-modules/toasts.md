# Toasts

The `kvision-toast` module contains ready to use functions to display toast messages - small, non-disruptive notification windows, which disappear automatically. To display a toast message just call one of the `success`, `info`, `warning` and `error` functions of the `Toast` object. 

```kotlin
Toast.info("This is a toast message")

Toast.error("There was an error!", "Error message")
```

You can also pass `ToastOptions` parameter, to configure how the toast message is displayed.

```kotlin
Toast.success(
    "This is a toast message",
    options = ToastOptions(
        positionClass = ToastPosition.TOPLEFT,
        progressBar = true,
        closeButton = true,
        timeOut = 10000
    )
)
```

