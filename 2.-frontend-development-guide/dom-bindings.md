# DOM Bindings & Lifecycle Hooks

KVision is based on [Snabbdom](https://github.com/snabbdom/snabbdom) - a JavaScript virtual DOM implementation. Via this implementation, components can directly access the browser DOM. Every KVision component is bound to the DOM tree after it has been rendered. You can get access to the underlying  `HTMLElement` object with the `getElement()` method from the `Widget` class.

A component lifecycle does not coincide with the lifecycle of DOM nodes. Nodes (mostly HTML elements) can be created and destroyed by Snabbdom at any time. You can use KVision hooks or override lifecycle methods to access DOM in a predictable manner.

### Lifecycle Hooks

* `afterCreate()` - called after the Virtual DOM node has been created
* `afterInsert()` - called after browser DOM node has been created and inserted into the DOM (rendered in the browser)
* `afterDestroy()`- called after browser DOM node has been removed from the DOM

The last two are most important. There are two helper methods `addAfterInsertHook()` and `addAfterDestroyHook()`, which allow your code to hook into widget life cycle without inheritance.

{% hint style="info" %}
When the component makes direct changes to the DOM after being rendered, you should make sure to do the cleanup after it's removed from the DOM.
{% endhint %}

The hooks functions are generally called, when new DOM nodes are created, inserted or removed from the DOM tree. Snabbdom is optimizing the rendering process - it will re-use DOM nodes whenever possible - no hooks will be called in such case. It can be a problem, because sometimes you need to access the specific HTML element (e.g. to add some low level handlers). Snabbdom has an option to mark virtual nodes with unique keys. In KVision this marking is easily enabled with `useSnabbdomDistinctKey()` protected method from the `Widget` class. If you have problems with component lifecycle hooks - just call this method in your own component `init` block (the `addAfterInsertHook()` function calls this method internally).

### Disposing

Every component is disposed when it is no longer used. You should override `dispose()` method or use `addBeforeDisposeHook()` if you need to free some resources. In particular a parent container is responsible for disposing its children components.

### Controlling Rendering

If making a lot of DOM changes at once, it is useful to wrap those changes in either of the singleRender{} or singleRenderAsync{} wrapper functions This will stop Snabbdom from applying every single change and just make all changes at once at the conclusion of the block.
