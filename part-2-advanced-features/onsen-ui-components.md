# Onsen UI components

[Onsen UI](https://onsen.io) is a set of mobile friendly web components, created with native iOS and Android design standards. With Onsen UI it's possible to develop mobile sites or hybrid web applications \(e.g. with Cordova\), which share exactly the same code, but also automatically choose the look and feel based on the platform on which they are running.

KVision supports all Onsen UI components with fully type-safe, consistent Kotlin API and readable DSL builders, but you should get familiar with [Onsen UI documentation](https://onsen.io/v2/api/js/) to achieve best results. You will also find a lot of examples in the KVision [Onsen UI kitchensink](https://github.com/rjaros/kvision-examples/tree/master/onsenui-kitchensink) example project.

To use Onsen UI with KVision you have to add `kvision-onsenui` and `kvision-onsenui-css` modules to your dependencies in `build.gradle.kts` file. Optionally you can include your own, custom CSS files to your `index.html` file instead of using css module.

{% hint style="info" %}
Note: Onsen UI components are not compatible with Bootstrap, so you can't mix both Onsen UI and Bootstrap KVision components in one application.
{% endhint %}

## Main layout containers

Onsen UI application layout is created with pages contained within three main types of components:

* Navigator
* Splitter
* Tabbar

KVision provides a dedicated DSL to define these layouts and to allow joining them together.

{% hint style="info" %}
Note: Layout containers are managed directly by Onsen UI library and should not be modified by KVision application. In particular, you should not use KVision data bindings \(e.g. with Redux store\) to render Onsen UI layout containers. However you can safely render content of pages or tabs.
{% endhint %}

### Navigator

A `Navigator` component allows you to define a stack of pages, where only the top level page is visible on the screen. The first page defined \(without ID parameter\) is visible on start. All other pages should have unique ID values and will not be visible until pushed with `pushPage` method of the `Navigator` component \(other methods like `insertPage` or `replacePage` can be used as well\). You can use the `BackButton` default action or `popPage` method of the `Navigator` component to return to the previous visible page. With `Navigator` component constructor parameters or `pushPage` method parameters you can specify additional effects and animations.

```kotlin
navigator(forceSwipeable = true) {
    page {
        toolbar("Page 1")
        onsButton("Go to page 2").onClick {
            this@navigator.pushPage("page2")
        }
    }
    page("page2") {
        toolbar("Page 2") {
            left {
                backButton("Page 1")
            }
        }
    }
}
```

### Splitter

A `Splitter` component enables responsive layout by implementing a two-column layout or an additional sliding menu. You need to add exactly two components to a `Splitter` container - a `SplitterSide` and a `SplitterContent`. The first one defines a sliding menu and the second defines main content. A menu may be always visible, always collapsed or may be collapsed based on the device orientation \(portrait or landscape\). Within `SplitterContent` component you can define one or more pages \(with unique IDs\). You can use `load` method of the `SplitterContent` class to select visible page.

```kotlin
splitter {
    lateinit var splitterContent: SplitterContent
    splitterSide(collapse = Collapse.COLLAPSE, swipeable = true) {
        page {
            ul {
                li("First page").onClick {
                    splitterContent.load("1")
                    this@splitterSide.close()
                }
                li("Second page").onClick {
                    splitterContent.load("2")
                    this@splitterSide.close()
                }
                li("Third page").onClick {
                    splitterContent.load("3")
                    this@splitterSide.close()
                }
            }
        }
    }
    splitterContent = splitterContent {
        page("1") {
            toolbar("First page")
        }
        page("2") {
            toolbar("Second page")
        }
        page("3") {
            toolbar("Third page")
        }
    }
}
```

### Tabbar

A `Tabbar` component allows you to define a list of tabs, where only one tab is visible at a time. You can define one or more `Tab` components inside a `Tabbar`. Every `Tab` should have a label, an icon or both. By default tabbar is displayed at the bottom of the screen. You can use `TabPosition.AUTO` to have it at the bottom on iOS and at the top on Android devices.

```kotlin
tabbar(position = TabsPosition.AUTO, swipeable = true) {
    tab("First tab") {
        page {
            div("Page 1")
        }
    }
    tab("Second tab") {
        page {
            div("Page 2")
        }
    }
}
```

## OnsenUi global object

Using `OnsenUi` global object you get access to various methods, which allow your code to detect platform, screen orientation, customize global animations settings and define global handler for device back button.

```kotlin
import io.kvision.onsenui.OnsenUi

if (OnsenUi.isAndroid()) {
    OnsenUi.disableAnimations()
}

OnsenUi.onOrientationChange { evt ->
    // handle orientation change event
}

OnsenUi.setDefaultDeviceBackButtonListener { evt ->
    // handle device back button event
}
```

## Form components

OnsenUI provides a rich set of form components and they are fully integrated with KVision standard forms  implementation. You can use all components from `io.kvision.onsenui.form` package as a standalone input elements or use them together with [`FormPanel`](forms.md) container.

```kotlin
@Serializable
data class MyForm(
    val text: String?,
    val password: String?,
    val number: Int?,
    val number2: Int?,
    val date: Date?,
    val check: Boolean = false,
    val select: String?,
    val search: String?
)

formPanel<MyForm> {
    add(MyForm::text, OnsText(placeholder = "Enter text", floatLabel = true))
    add(
        MyForm::password,
        OnsText(
            type = TextInputType.PASSWORD,
            placeholder = "Enter password",
            floatLabel = true
        )
    )
    add(
        MyForm::number,
        OnsNumber(
            placeholder = "Enter number",
            floatLabel = true
        ), validator = {
            (it.value ?: 0).toInt() > 5
        }
    )
    add(
        MyForm::number2,
        OnsRange(min = 1, max = 10, label = "Range selection"),
        validator = {
            (it.value ?: 0).toInt() > 5
        }
    )
    add(
        MyForm::date,
        OnsDateTime(mode = DateTimeMode.TIME, label = "Enter time")
    )
    add(
        MyForm::check, OnsSwitch(label = "Check option")
    )
    add(
        MyForm::select,
        OnsSelect(
            listOf("1" to "Option 1", "2" to "Option 2", "3" to "Option 3"),
            emptyOption = true,
            multiple = true,
            label = "Select option from a list"
        )
    )
    add(
        MyForm::search,
        OnsText(
            TextInputType.SEARCH,
            label = "Search",
            placeholder = "Search"
        )
    )
    onsButton("Show data").onClick {
        console.log(this@formPanel.getData())
    }
    onsButton("Clear data").onClick {
        this@formPanel.clearData()
    }
    onsButton("Validate").onClick {
        this@formPanel.validate()
    }
}
```

## Dialog components

Dialog components include ActionSheets, Alerts, Dialogs, Modals, Popovers and Toasts. To show one of these components you need o create an instance of the corresponding class from `io.kvision.onsenui.dialog` package, and call one of `show*` methods.

```kotlin
val alertDialog = alertDialog("Alert", true, rowfooter = true) {
    div("Lorem Ipsum")
    alertDialogButton("OK")
    alertDialogButton("Cancel")
}
alertDialog.showDialog()

val modal = modal {
    div("Modal content")
    onsButton("Close").onClick {
        this@modal.hideModal()
    }
}
modal.showModal()

val dialog = dialog(true) {
    div("Dialog content")
    onsButton("Close").onClick {
        this@dialog.hideDialog()
    }
}
dialog.showDialog()

val actionSheet = actionSheet("Test action", true) {
    actionSheetButton("First", icon = "md-square-o").onClick {
        this.icon = "md-check-square"
    }
    actionSheetButton("Second", icon = "md-square-o")
    actionSheetButton("Close", icon = "md-close").onClick {
        this@actionSheet.hideActionSheet()
    }
}
actionSheet.showActionSheet()

val toast = toast(animation = ToastAnimation.FALL) {
    div("This is a toast")
    onsButton("OK").onClick {
        this@toast.hideToast()
    }
}
toast.showToast()

val popover = popover(cancelable = true) {
    div("Test of the popover")
}
popover.showOnsPopover(widget) // attach popover to a widget
```

You can also use one of the top level functions: `showAlert`, `showConfirm`, `showPrompt`, `showToast`, `showActionSheet` to display popup windows without creating any objects.

```kotlin
showToast(
    "Notification message",
    "OK",
    timeout = 2000,
    animation = ToastAnimation.FALL
)
```

## Infinite scroll

With Onsen UI you can create infinite scroll effect in two different ways.

### Load more

Use this way if you want to load content asynchronously \(e.g. from a network\). You should use `onInfiniteScroll` event handler of the page component.

```kotlin
page {
    onsList().bind(InfiniteScrollModel.items) { items ->
        items.forEach {
            item("Item #$it")
        }
    }
    div(className = "after-list") {
        icon("fa-spinner", size = "26px", spin = true)
    }
    onInfiniteScroll { done ->
        window.setTimeout({
            InfiniteScrollModel.loadMore()
            done()
        }, 600)
    }
}
```

### Lazy repeat

Use this way if you have very long list of items, but you don't want to add all of them to the DOM at the same time. Onsen UI will automatically unload items that are not visible.

```kotlin
page {
    onsList {
        onsLazyRepeat(3000) { index ->
            OnsenUi.createElement("<ons-list-item>Item #$index</ons-list-item>")
        }
    }
}
```

{% hint style="info" %}
Note: You can't use KVision components with lazy repeat list. You need to create native DOM elements with `OnsenUi.createElement` helper method.
{% endhint %}

 

