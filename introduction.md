# Introduction

[KVision](https://kvision.io) is an open source web framework created for [Kotlin](https://kotlinlang.org/) language. It allows developers to build modern web applications in Kotlin.

## Main features

#### Compiled and strongly typed programming language

Kotlin is a modern programming language released in 2016 by JetBrains. It's a statically typed language with many great, practical features and outstanding tooling support (IntelliJ IDEA).

#### Easy to use

KVision allows you to build modern web applications with the Kotlin language, without any use of HTML, CSS or JavaScript.

KVision's basic design is quite similar to many non-web UI programming libraries including Swing, JavaFX, QT and WinForms.

#### **Ready to use components**

It gives you a hierarchy of almost 100 ready to use GUI components, which can be used as a builder blocks for the application UI. Hundreds of features are available with easy to learn and consistent API.

* sophisticated containers (tabs, stack, dock, grid, horizontal, vertical, flexbox, responsive)
* forms with type-safe data model and built-in validation
* many different text input components including rich text editor, typeahead and input mask support
* buttons, checkboxes, radios and switches
* date and time picker
* spinner, range and numeric input components
* advanced select box with remote data support
* file upload with preview and multi-selection
* advanced charts
* reactive tables
* data binding components and observable data sources
* navigation bar, toolbar, context menu and offcanvas sidebar
* tooltips and popovers
* modals including ready to use alerts and confirm dialogs
* floating, re-sizable windows
* configurable toasts
* many HTML components including tables, lists, images, canvas and iframe
* theme switcher for dark mode with auto-detection
* built-in support for [Handlebars](http://handlebarsjs.com) templates
* built-in support for [Font Awesome](https://fontawesome.com/) icons
* built-in support for [Bootstrap Icons](https://icons.getbootstrap.com/)
* built-in support for [ReduxKotlin](https://reduxkotlin.org/)
* built-in support for [Material 3](https://material-web.dev/) web components
* built-in support for [React](https://reactjs.org/) components
* built-in support for [Onsen UI](https://onsen.io/) web components
* built-in support for [Pace](https://codebyzach.github.io/pace/) automatic page loader
* built-in support for [Leaflet](https://leafletjs.com/) interactive maps
* built-in support for printing with [Print.js](https://printjs.crabbly.com/) library
* built-in support for [Ballast](https://copper-leaf.github.io/ballast/) state management framework

#### Flexibility

KVision was designed to be open and flexible. By default it gives you [Bootstrap](https://getbootstrap.com/) based look & feel. You can also use themes from [Bootswatch](https://bootswatch.com/) or you can disable them all and design your application appearance from the scratch, limited only by your own knowledge of CSS.

KVision fully supports both reactive and imperative programming models. It gives you everything you may need for the state management of your apps. From simple observables to advanced redux stores and bindings to coroutines StateFlow. From simple event callbacks to functional event flows.

KVision is open source and modular. You can create your own modules taking an example from quite a few already existing. Almost all KVision classes are declared as open. With inheritance and composition you can build your own components, with all new features you need in your apps.

KVision is suitable for any kind of projects, including responsive, mobile web applications or even a simple, plain websites.

#### **Fullstack**

KVision contains innovative connectivity interface for a bunch of popular server side frameworks - [Ktor](https://ktor.io), [Jooby](https://jooby.io), [Spring Boot](https://spring.io/projects/spring-boot), [Javalin](https://javalin.io), [Vert.x](https://vertx.io) and [Micronaut](https://micronaut.io), which allows to build fullstack applications with shared code for data model and business logic. KVision closely integrates the client and the server side of the project with a shared data model and fully type-safe connectivity between both sides (based on automatically generated routings and JSON-RPC endpoints). This architecture is based on Kotlin coroutines, wrapping asynchronous client-server calls into easy-to-read synchronous-like code. With the help of the dedicated Kotlin compiler plugin, based on [KSP](https://kotlinlang.org/docs/ksp-overview.html) library, you have to write only essential, boilerplate-free code. This makes KVision full-stack applications very easy to create and maintain.

There is also support for type-safe websocket connections and SSE (server-sent events), based on [Kotlin coroutines channels](https://kotlinlang.org/docs/reference/coroutines/channels.html).

#### Other features

* Utilizes [Snabbdom](https://github.com/snabbdom/snabbdom) fast virtual DOM implementation
* Type safe DSL builders
* Event Flows
* Internationalization support based on [gettext](https://www.gnu.org/software/gettext/) translations and [gettext.js](https://github.com/guillaumepotier/gettext.js) library.
* Support for jQuery animations end effects
* Drag & drop support
* Type-safe REST connectivity
* Integrated JS router
* Support for building hybrid, mobile applications for Android and iOS with [Apache Cordova](https://cordova.apache.org/).
* Support for building cross-platform, desktop applications with [Electron](https://electronjs.org/).
* Compatible with modern browsers (MS Edge, Firefox, Chrome, Safari, Opera)
* [Karma](https://karma-runner.github.io/) testing framework support

