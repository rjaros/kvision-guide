# Internationalization

## Features

* Automatic language detection \(based on the browser language settings\).
* Dynamic language change with automatic re-rendering of the whole application GUI.
* Multi-language support for built-in complex components:

  * datetime control - 46 languages
  * select control - 12 languages
  * upload control - 39 languages
  * richtext editor - 2 languages

  Contributions are welcomed.

* Minimal impact of internationalization process on the application source code.
* Automatic extraction of text to be translated from the application code.
* Support for well known translation files format \(gettext `*.po` files\).

## Requirements

To create a multi-language application you have to add the `kvision-i18n` module as a dependency for your project.

```groovy
dependencies {
// ...
    compile "io.kvision:kvision-i18n:${kvisionVersion}"
// ...
}
```

## Using multi-language text in application sources

To mark some text for translation just use one of the four available helper methods from the `io.kvision.i18n.I18n` object instead of plain string literals.

| Method | Description |
| :--- | :--- |
| tr | Dynamic translation for singular form. |
| ntr | Dynamic translation for plural forms. |
| gettext | Static translation for singular form. |
| ngettext | Static translation for plural forms. |

Dynamic translations are bound to the components they are part of \(tag content, button text etc.\). They are dynamically changed when the current language for the application is changed.

```kotlin
// ...
import io.kvision.i18n.tr
// ...

span(tr("Label text"))
button(tr("Button text"))
```

Static translations are evaluated only when the helper method is called. They can be used to translate text not bound to any KVision component.

```kotlin
// ...
import io.kvision.i18n.gettext
// ...

console.log(gettext("Some info message"))
```

KVision has support for plural language forms, so you can use `ntr` or `ngettext` methods when plural forms are necessary.

```kotlin
// ...
import io.kvision.i18n.I18n.ntr
// ...

val count = 5
hPanel {
    span("$count")
    span(ntr("file", "files", count))
}
```

## Translation files

Until you create and initialize translation files for some other language, your application will use string literals used in the source code. It's probably a good practice to use English literals in your code and other languages in the translation files.

To generate basic translation files run the command:

```text
./gradlew generatePotFile                                (on Linux)
gradlew.bat generatePotFile                              (on Windows)
```

This command will search your sources for any usages of internationalization methods \(`tr` or others\) and generate a `messages.pot` file in the `src/main/resources/i18n` directory. This file is the base for your translations. For any language you would like to support, copy the `messages.pot` file to `messages-XX.po`, where XX is a country code \(en, de, es, fr etc.\). These files should be translated according to the [PO format specification](https://www.gnu.org/software/gettext/manual/html_node/PO-Files.html). You can use many popular tools for editing PO files to simplify the translation process.

{% hint style="info" %}
You should correctly set `Language` and `Plural-Forms` headers of your PO files.
{% endhint %}

After adding some new texts to your sources you can call the `./gradlew generatePotFile` task to refresh the `messages.pot` file. You can then use the [`msgmerge`](https://www.gnu.org/software/gettext/manual/html_node/msgmerge-Invocation.html) tool from the GNU gettext package to merge new keys with existing translation files. You can also add new `msgid` and `msgstr` lines to your translation files by hand.

## Initializing translations

To initialize translations for one or more languages, you need to initialize the `I18n.manager` property during application initialization \(preferably in the `start` method of your `Application` object\). You should add all supported languages to the map given as the parameter to the `DefaultI18nManager` constructor.

```kotlin
class Helloworld : Application() {
    override fun start() {
        // ...
        I18n.manager =
                DefaultI18nManager(
                    mapOf(
                        "en" to require("i18n/messages-en.json"),
                        "pl" to require("i18n/messages-pl.json"),
                        "de" to require("i18n/messages-de.json")
                    )
                )
        // ...
    }
}
```

## Changing the current language

The current language is bound to the `I18n.language` property. This property is initialized with the default country code from the browser language settings. It can be changed at any time from your code.

```kotlin
select(listOf("en" to "English", "de" to "Deutsch"), I18n.language) {
    onEvent {
        change = {
            I18n.language = self.value ?: "en"
        }
    }
}
```

