# Building with Apache Cordova

KVision allows you to develop hybrid, mobile applications \(for Android and iOS\) with the [Apache Cordova](https://cordova.apache.org/) framework. You can use all KVision components in your mobile applications, and the kvision-cordova module gives you a complete set of Kotlin language bindings for core Cordova API. You can develop your applications in a productive way, with Webpack's HMR and instantaneous hot reload of your application code in the phone emulator.

{% hint style="info" %}
Note: We aim to support both Android and iOS platforms. But at the time of writing only the Android platform and toolchain were properly tested.
{% endhint %}

KVision adds Kotlin type-safe bindings for most of core Cordova API, but you should get familiar with [Cordova documentation](https://cordova.apache.org/docs/en/latest/) to achieve best results.

## Requirements

Before using KVision with Apache Cordova you have to install [Cordova CLI](https://cordova.apache.org/docs/en/latest/guide/cli/index.html#installing-the-cordova-cli). You will use `cordova` command the same way as in standard Cordova applications \(JavaScript based\).

### Android platform

To develop for Android platform you have to install Android SDK. See [Cordova documentation](https://cordova.apache.org/docs/en/latest/guide/platforms/android/index.html#installing-the-requirements) for details. Make sure you have set correctly all required environment variables.

### iOS platform

To develop for iOS platform you have to install Xcode, and it's possible only on the OS X operating system on Intel-based Macs. See [Cordova documentation](https://cordova.apache.org/docs/en/latest/guide/platforms/ios/index.html#installing-the-requirements) for details.

## Getting started

The easiest way to start is to clone `template-cordova` project from [kvision-examples](https://github.com/rjaros/kvision-examples) repository on GitHub. It contains the basic configuration for building and running your application.

This template is just a typical KVision application with some additional files and directories required for Cordova framework. You need to initialize Cordova project with `cordova prepare` command. After this you are ready to build and run your app.

The recommended way to work with the project is using the emulator/simulator and running your application from your local Webpack dev server. This way, you get all the benefits of Webpack's HMR and hot reload. Just like with the standard KVision app, you should run `./gradlew -t run` command in the dedicated terminal window. When the webpack dev server is running, you should execute `cordova emulate` command inside the second terminal window. It will start the emulator/simulator and deploy your application inside the emulated device. The content of the main WebView will be served from your webpack dev server, so it will be reloaded when you make some changes to your source code. 

{% hint style="info" %}
Hint: when developing for the Android platform you can connect Chrome Dev Tools to your emulated phone and get full access \(DOM, console, network, sources, storage etc.\) to your app. Just open  [chrome://inspect/](chrome://inspect/#devices) URL in your Chrome browser and select your device.
{% endhint %}

## Cordova configuration

Cordova project configuration is saved inside `config.xml` and `package.json` files. The template project includes all core [Cordova plugins](https://cordova.apache.org/docs/en/latest/#plugin-apis).

{% code-tabs %}
{% code-tabs-item title="package.json" %}
```javascript
{
  "cordova": {
    "plugins": {
      "cordova-plugin-whitelist": {},
      "cordova-plugin-device": {},
      "cordova-plugin-battery-status": {},
      "cordova-plugin-camera": {},
      "cordova-plugin-dialogs": {},
      "cordova-plugin-network-information": {},
      "cordova-plugin-screen-orientation": {},
      "cordova-plugin-splashscreen": {},
      "cordova-plugin-statusbar": {},
      "cordova-plugin-vibration": {},
      "cordova-plugin-file": {},
      "cordova-plugin-geolocation": {},
      "cordova-plugin-inappbrowser": {},
      "cordova-plugin-media": {},
      "cordova-plugin-media-capture": {}
    }
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can use `cordova plugin remove` command to remove unused plugins or `cordova plugin add` command to add new plugins.

{% hint style="info" %}
Note: kvision-cordova module offers Kotlin bindings for all core Cordova plugins. You can also use any [external Cordova plugin](https://cordova.apache.org/plugins/) in your application, but you need to manage JS interoperation on your own. You can declare external classes or use dynamic types, whatever suits you more. 
{% endhint %}

Currently the template project targets only the Android platform. You can use `cordova platform add ios` command to add iOS target.

