# W3C, Snabdom, and KVision Elements

There are three sets of classes which you may need to use to interact with components.&#x20;

## KVision

As discussed in this chapter, KVision provides many widgets, [all of which](basic-components.md) ultimately implement **Component**. Components are high level classes which allow you to build rich behavior, and for the most part to ignore the nitty gritty of the browser internals.

Occasionally, however, you'll need to hook more deeply into the browser internals. For example, maybe you've integrated with a third party Javascript library which needs (or provides) access to Element objects. To do this, you'll need to make use of W3C classes.

## W3C (and using asDynamic())

Jetbrains have helpfully built out the majority of the browser APIs as part of the stdlib, packaged as kotlin-stdlib-js; the main representation of an element is the **Element** class, which is documented here: [https://kotlinlang.org/api/latest/jvm/stdlib/org.w3c.dom/-element/](https://kotlinlang.org/api/latest/jvm/stdlib/org.w3c.dom/-element/). Element is subtasked by **HTMLElement**, which provides additional properties and functions;  as long as you're rendering HTML (which you are), you can cast directly from Element to HTMLElement to get access to these. (The hierarchy is documented here: [https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement)).

However, this API isn't 100% complete (mostly because of Javascript's shape-shifting capabilities), and sometimes you'll need to make use of the _asDynamic()_ extension function to use properties that aren't available on the API.

For example, let's say that you're handling a file upload.  Naturally, you'll want to obtain the uploaded files from the event - one way of doing so in Javascript would be to access the "files" property of the change event. Unfortunately this won't work directly in Kotlin:

```
element.addEventListener("change", { event ->
    event.target.files // compile error
})
```

This is where you can make use of asDynamic(), as follows:

```
element.addEventListener("change", { event ->
    event.target.asDynamic().files // works!
})
```

## Snabbdom

As discussed in the prior chapter, Snabbdom provides both a virtual DOM and the [main component lifecycle methods](dom-bindings.md). When using these Snabbdom lifecycle functions, sometimes you'll be using its representation of DOM elements, **VNode** (documented here: [https://github.com/snabbdom/snabbdom](https://github.com/snabbdom/snabbdom)). For example, if you use _addAfterInsertHook,_ you'll be passed a VNode representing the element.

## Converting between APIs

So, how do you work across these APIs?

The **Component** class provides _getElement()_, which gives you a nullable w3c HTMLElement instance. However, it's important to understand that if your Component hasn't yet been rendered (and, rendering does _not_ necessarily take place as soon as you _add()_ the Component to a Container), then you'll receive null as a result from getElement(). If your code is handling a DOM event _from_ the component, then it's a safe bet that the Element will be non-null, and you can go ahead and add the behavior you need to it.

However, if you need access to the Element during component setup, then this is where Snabbdom's lifecycle functions come into play. For example, let's say we want to get access to component geometry to figure out how to render something else.

```
val myComponent = // some component written using KVision 

myComponent.addAfterInsertHook { vnode ->

   // possibility 1
   val height = myComponent.getElement()!!.offsetHeight
      
   // possibility 2
   val height = vnode.elm!!.unsafeCast<HTMLElement>().offsetHeight
```

Here, we're making use of _addAfterInsertHook_ to be notified once the component has been added to the DOM. We've then got two possibilities for accessing the low level HTMLElement; either we can call getElement() on the component itself, or we can cast the "elm" that's available on the Snabbdom vnode instead - which you use is just a matter of taste, as they are fully equivalent.
