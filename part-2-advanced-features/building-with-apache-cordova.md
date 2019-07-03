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
    },
    "platforms": [
      "android"
    ]
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

## Cordova API

Most of the original, callback-based Cordova API has been wrapped into simple suspending functions. They can be used with the full power of Kotlin coroutines, although the Cordova's usage patterns are closely followed, so the standard Cordova documentation should give enough knowledge to work with KVision.

A special `Result<V, E: Exception>` class is used whenever possible to handle both success and failure of Cordova API function call.

### Device

This plugin allows to get information about the device, and also add listeners for standard [Cordova events](https://cordova.apache.org/docs/en/latest/cordova/events/events.html).

```kotlin
GlobalScope.launch {
    console.log(getDevice())
}
addResumeListener { resumeEvent ->
    console.log(resumeEvent)
}
addCordovaEventListener(CordovaEvent.BACKBUTTON) {
    console.log("backbutton")
}
addCordovaEventListener(CordovaEvent.VOLUMEDOWNBUTTON) {
    console.log("volume down")
}
addCordovaEventListener(CordovaEvent.VOLUMEUPBUTTON) {
    console.log("volume up")
}
```

{% hint style="info" %}
All Cordova API is available only after `deviceready` event is dispatched. Most of KVision functions already handle this event internally, so usually there is no need to wrap your code into `deviceready` listener. 
{% endhint %}

### Battery status

This plugin allows you to get information about the current battery level.

```kotlin
Battery.addStatusListener(BatteryEvent.BATTERY_STATUS) {
    console.log("Battery level: it.level")
}
Battery.addStatusListener(BatteryEvent.BATTERY_LOW) {
    console.log("Battery low level: ${it.level}")
}
Battery.addStatusListener(BatteryEvent.BATTERY_CRITICAL) {
    console.log("Battery critical level: ${it.level}")
}
```

### Camera

This plugin allows you to use the phone camera to get a picture or a video.

```kotlin
button("Take a photo", "fa-camera") {
    onClick {
        GlobalScope.launch {
            val result = Camera.getPicture(
                CameraOptions(
                    mediaType = Camera.MediaType.PICTURE,
                    destinationType = Camera.DestinationType.FILE_URI
                )
            )
            processCameraResult(result)
            Camera.cleanup()
        }
    }
}
Camera.addCameraResultCallback {
    processCameraResult(it)
}
fun processCameraResult(result: Result<String, CameraException>) {
    result.success {
        GlobalScope.launch {
            File.resolveLocalFileSystemURLForFile(it).success {
                store.dispatch(ImageAction.Image(it.toInternalURL()))
            }
        }
    }
    result.failure {
        store.dispatch(ImageAction.Error(it.message ?: "No data"))
    }
}
```

{% hint style="info" %}
When using the camera on the Android platform, use `addCameraResultCallback` method to correctly support [Android lifecycle](https://cordova.apache.org/docs/en/dev/guide/platforms/android/index.html#lifecycle-guide).
{% endhint %}

### Dialogs

The dialog plugin allows to display native notifications - alert, prompt and confirmation dialog windows and also the beep sound.

```kotlin
Notification.alert("The message", "Title", "OK") {
    console.log("You pressed OK")
}
Notification.confirm("Are you sure?", "Delete", listOf("Yes", "No")) { index ->
    console.log("You pressed button number $index")
}
Notification.prompt("Enter your name:", "Register", listOf("OK", "Cancel"), "John Snow") { res ->
    console.log("You pressed button number ${res.buttonIndex} and your name is ${res.input1}")
}
Notification.beep(3)
```

### Network information

This plugin allows to get information about the current network connection and add listeners for the network status events. 

```kotlin
GlobalScope.launch {
    console.log(Network.getConnectionType().toString())
}
Network.addStatusListener(NetworkEvent.OFFLINE) {
    console.log("Going offline")
}
Network.addStatusListener(NetworkEvent.ONLINE) {
    console.log("Going online")
}
```

### Screen orientation

This plugin allows to get current screen orientation, lock and unlock the specified orientation and also add listeners for the screen orientation change events.

```kotlin
console.log(Screen.getOrientation())

button("Lock landscape").onClick {
    Screen.lock(Screen.Orientation.LANDSCAPE)
}
button("Lock portrait").onClick {
    Screen.lock(Screen.Orientation.PORTRAIT)
}
button("Unlock").onClick {
    Screen.unlock()
}
Screen.addOrientationChangeListener {
    console.log("Orientation changed to ${it}")
}
```

### Splash screen

Cordova allows to configure a splash screen for your application, which is displayed before the application is fully loaded. Check [Cordova documentation](https://cordova.apache.org/docs/en/dev/reference/cordova-plugin-splashscreen/index.html) for details. This plugin has also simple methods to show and hide the splash screen at runtime.

```kotlin
button("Show splash screen for 3 seconds").onClick {
    Splashscreen.show()
    window.setTimeout({
        Splashscreen.hide()
    }, 3000)
}
```

### Status bar

This plugin allow to style the native status bar with a few predefined color schemes.

```kotlin
button("Show status bar").onClick {
    StatusBar.show()
}
button("Hide status bar").onClick {
    StatusBar.hide()
}
button("Set overlay").onClick {
    StatusBar.overlaysWebView(true)
}
button("Set no overlay").onClick {
    StatusBar.overlaysWebView(false)
}
button("Set style to light content").onClick {
    StatusBar.styleLightContent()
}
button("Set style to black opaque").onClick {
    StatusBar.styleBlackOpaque()
}
button("Set style to black translucent").onClick {
    StatusBar.styleBlackTranslucent()
}
button("Set style to default").onClick {
    StatusBar.styleDefault()
}
button("Set background color").onClick {
    StatusBar.setBackgroundColorByHexString("#88FF0000")
}
```

### Vibration

This plugin provides a way to vibrate the device with a single vibration or with a given pattern 

```kotlin
button("Vibrate for 1 second").onClick {
    Vibration.vibrate(1000)
}
button("Vibrate with a given pattern").onClick {
    Vibration.vibrate(1000, 2000, 1000, 2000)
}
```

### File

The File plugin provides a comprehensive API to access the device file system. KVision bindings for the original, callback based API are designed as suspending functions and suspending extension functions.

```kotlin
GlobalScope.launch {
    // Get external data directory root
    File.getSystemDirectories().externalDataDirectory?.toDirectoryEntry()?.success { root ->
        // Print root directory information.
        console.log(root)
        root.getMetadata().success { console.log(it) }
        // List root directory entries.
        root.readEntries().success {
            console.log(it)
        }
        // Create new file from a Blob.
        root.getFile("test.txt").success { it.write(Blob(arrayOf("A test content."))) }
        // List root directory entries (with new file on the list).
        root.readEntries().success {
            console.log(it)
        }
        // Read file content as text.
        root.getFile("test.txt").success { console.log(it.readAsText()) }
        // Append content to the file.
        root.getFile("test.txt").success { it.append(Blob(arrayOf("two"))) }
        // Access the file.
        root.getFile("test.txt").success {
            // Read file content as a buffer.
            val buf = it.readAsArrayBuffer().component1()
            // Create a blob from a buffer.
            val blob = Blob(arrayOf(Uint8Array(buf!!)))
            // Write blob to a new file.
            root.getFile("test2.txt").success {
                it.write(blob)
            }
        }
        // Access the file.
        root.getFile("test.txt").success {
            // Get native file URL.
            console.log(it.toURL())
            // Get Cordova cdvfile:// file URL.
            console.log(it.toInternalURL())
        }
    }
}
```

