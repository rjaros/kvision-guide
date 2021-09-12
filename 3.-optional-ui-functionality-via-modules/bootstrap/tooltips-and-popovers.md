# Tooltips and popovers

Every component in KVision can display a tooltip and a popover, both are based on Bootstrap components. Due to Bootstrap 5 limitation, components can't have both tooltip and popover enabled at the same time.

{% hint style="info" %}
This components are only available with the `kvision-bootstrap` module.
{% endhint %}

## Tooltips

A tooltip is a small informational text component usually displayed when a mouse pointer stops over the given component \(you can also change the default trigger\). By default the tooltip content is taken from the `title` property of the component.

To activate the tooltip handler use the `enableTooltip` method from the `Widget` class.

```kotlin
button("A button") {
    title = "This is a tooltip"
    enableTooltip()
}
```

Use `disableTooltip` to deactivate the handler.

The `enableTooltip` method takes the `TooltipOptions` object as a parameter. It allows you to specify the alternative text if the `title` property is not set, specify if the content contains HTML formatting, disable animation, set the tooltip delay, the placement and the list of trigger events \(by default tooltips are triggered by hover and focus events\).

```kotlin
button("A button") {
    enableTooltip(TooltipOptions(
        title = "This is a <b>rich</b> tooltip",
        rich = true,
        delay = 1000,
        placement = Placement.BOTTOM,
        triggers = listOf(Trigger.CLICK, Trigger.HOVER)
    ))
}
```

You can use `Trigger.MANUAL` to disable automatic tooltip handling and then use the `showTooltip()` and `hideTooltip()` methods to control the visibility.

```kotlin
button("A button") {
    enableTooltip(TooltipOptions(
        title = "Manual tooltip",
        triggers = listOf(Trigger.MANUAL)
    ))
    onClick {
        this.showTooltip()
    }    
}
```

## Popovers

A popover is a small informational window usually displayed when a user clicks on a given component \(you can also change the default trigger\). Unlike a tooltip, a popover has a title and content. By default the title is taken from the `title` property of the component and the content must be given as a parameter.

To activate the popover handler use `enablePopover` method from the `Widget` class.

```kotlin
button("A button") {
    title = "This is a popover title"
    enablePopover(PopoverOptions(content = "This is a popover content"))
}
```

Use `disablePopover` to deactivate the handler.

The `enablePopover` method takes the `PopoverOptions` object as a parameter. It allows you to specify the alternative title text if the `title` property is not set, as well as set the content text, specify if the title and/or content contains HTML formatting, disable animation, set the popover delay, the placement and the list of trigger events \(by default popovers are triggered by a click event\).

```kotlin
button("A button") {
    enablePopover(PopoverOptions(
        title = "This is a <b>rich</b> popover title",
        content = "This is a <b>rich</b> popover content",
        rich = true,
        delay = 1000,
        placement = Placement.BOTTOM,
        triggers = listOf(Trigger.CLICK)
    ))
}
```

You can use `Trigger.MANUAL` to disable automatic popover handling and then use `showPopover()` and `hidePopover()` methods to control the visibility programmatically.

```kotlin
button("A button") {
    enablePopover(PopoverOptions(
        title = "Manual popover",
        content = "Manual popover content",
        triggers = listOf(Trigger.MANUAL)
    ))
    onEvent {
        dblclick = {
            self.showPopover()
        }
    }
}
```

