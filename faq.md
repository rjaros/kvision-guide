---
description: Frequently asked questions
---

# FAQ

## Why a new framework?

There are of course many different front-end frameworks on the market. A lot of them share many similarities, others offer some special and unique features. Probably most of them are plain JavaScript frameworks, but there are other languages and technologies in play as well. Just look at [TodoMVC](http://todomvc.com/) website to find them and learn about them.

I’ve been working with different technologies and frameworks for many years. I’ve used them in many commercial and non-commercial projects of different size and complexity. Most of the time I was more then satisfied with the tools I’ve been using. But I’ve never found a solution, I could just call perfect. There was always something missing and something was not fully correct.

I don’t really like to go with the mainstream. I like to explore and learn new possibilities. I try to make things easier and more productive. I like to reuse and integrate good stuff, made by other people and available as open source. I had many thoughts and conclusions about features a perfect framework should have. And finally I’ve decided to make my own framework. I plan to make it a perfect solution for me - it’s quite easy when you know the expectations ;-) But I hope it can be a good solution for other developers as well.

## Why the name "KVision"?

This framework is a great-great-grandson of [Turbo Vision](https://en.wikipedia.org/wiki/Turbo\_Vision). And K is for Kotlin of course.

## Is it similar to ...?

You can find some of the concepts and ideas implemented in KVision in:

* [Qooxdoo](https://www.qooxdoo.org/) - an universal JavaScript framework (object oriented architecture, layout containers, forms)
* [TornadoFX](https://tornadofx.io/) - JavaFX framework for Kotlin (DSL builders for UI)
* [Udash](https://udash.io/) - a Scala/Scala.js web framework (type-safe server side interfaces)

## What is the size of a typical application?

KVision applications are built with [Kotlin JavaScript DCE (dead code elimination)](https://kotlinlang.org/docs/reference/javascript-dce.html) so the size of an application depends on what components are used. Of course Kotlin run-time library and other required dependencies add an overhead, so you can't expect resulting code to be as small as with plain JavaScript projects. Currently the size of the smallest "Hello World" application is 162KB (41KB gzipped). The size of the "TodoMVC" is about 330KB (84KB gzipped) and the size of the "Showcase" application, which presents most of the framework features, is about 3245KB (792KB gzipped).

