# Setting Up

KVision applications are built with [Gradle](http://gradle.org/). The official Kotlin/JS gradle plugin is used to manage NPM dependencies, pack bundles (via [webpack](https://webpack.github.io/)) and test the application using [Karma](http://karma-runner.github.io/1.0/index.html). By using Gradle continuous build, you also can get hot module replacement feature (apply code changes in the browser on the fly).

## Requirements

To build a typical KVision application you should have some tools installed on your machine and available on the system PATH:

* [JDK](https://jdk.java.net/) 17 or higher
* [Git](https://git-scm.com) (with additional UNIX tools if using Windows)
* GNU [xgettext](https://www.gnu.org/software/gettext) and [msgmerge](https://www.gnu.org/software/gettext) utilities to use [Internationalization](../2.-frontend-development-guide/internationalization.md) features   &#x20;

{% hint style="info" %}
Note: Make sure you are building KVision applications on the local file system. &#x20;
{% endhint %}

##
