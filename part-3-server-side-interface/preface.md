# Preface

Web applications are typically composed of two parts - the client part, that works in the user browser, and the server part, that runs on some kind of a network server. The client part is responsible for creating GUI \(graphical user interface\) and interacting with the user. The server part takes care of the business logic \(data manipulation, data storage, math calculations, business processes and so on\). There is a huge number of different solutions, that allow developers to build such type of applications, but even the most popular, modern and well received suffer from some serious problems and limitations.

### Separate languages

The client side of the web application is most often built with JavaScript, HTML and CSS. This alone means three different programming languages. And the server side is usually built using yet another language e.g. Java, C\#, PHP or Python. This is a well known problem and the popular solution is to use Node.js on the server side and create full-stack applications with JavaScript only. It's nice, but it has it's own limitations \(single-threaded architecture, scalability, medium quality of many npm packages\) and is not satisfactory for everyone.

**Other solutions are based on modern JVM languages compiled to JavaScript \(Scala.js, Kotlin/JS\). These are the relatively best solutions - you get the quality of the enterprise class on the server side and the same language on the client side. This is how KVision works.**

### Separate, typeless endpoints

Both parts of the web application are typically completely separate from each other. On the server side an endpoint is just a routing definition \(an URL\) with some parameters and body parser. On the client side it's just a XmlHttpRequest \(or jQuery\) call. But, because of typeless natures of JavaScript and HTTP protocol, there is no strict interface, no contract, between the two. A change made on one side does not force the developer to make compatible change on the other side. Someone always has to remember about it. And the inevitable errors are expensive, because they appear only at runtime \(and often only in the production environments\).

**KVision is the only framework that allows you to define a type-safe and compile-time contract between the client and the server part of your application.**

### Callbacks

The asynchronous architecture of JavaScript environment and XmlHttpRequest forces developers to use callbacks when the client part communicates with the server. Callbacks make the code look complicated and hard to understand. With many callbacks you can even end up with so called ["callback hell"](https://en.wiktionary.org/wiki/callback_hell). You can keep some [guidelines](http://callbackhell.com/) or you can try to use promises / async functions, but a big change is only introduced by the Kotlin coroutines.

**The architecture of KVision server-side interfaces is heavily based on coroutines, wrapping asynchronous client-server callbacks into easy-to-read synchronous-like code.**

