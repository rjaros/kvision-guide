# Building with Electron

KVision allows you to develop cross-platform \(Windows, Linux and Mac\), desktop applications with the full power of [Electron](https://electronjs.org) framework.  You can use all KVision components in your desktop applications, and the kvision-electron module gives you a complete set of Kotlin language bindings for Electron API.

### Setting up

The easiest way to start is to clone `template-electron` project from [kvision-examples](https://github.com/rjaros/kvision-examples) repository on GitHub. It contains the basic configuration for building, running and bundling your application. To run your application during the development call:

```text
./gradlew runApp                                    (on Linux)
gradlew.bat runApp                                  (on Windows)
```

It will open an Electron window with your application running inside.

To bundle your application call:

```text
./gradlew bundleApp                                    (on Linux)
gradlew.bat bundleApp                                  (on Windows)
```

It will create packed and unpacked app in the `build/electron` directory.

{% hint style="info" %}
If your application doesn't use any NodeJS or Electron API, you can use standard way of development, inside your favorite web browser.
{% endhint %}

### Configuring Electron builder

The application is build with [electron-builder](https://www.electron.build/) tool. The configuration is contained in the `src/main/electron/electron-builder.yml` configuration file. You can find documentation of electron-builder [here](https://www.electron.build/configuration/configuration). By default the application is built for the current platform only. Cross-platform build \(e.g. for Windows on Linux host\) is sometimes possible, but there are some [additional requirements](https://www.electron.build/multi-platform-build).

### Using NodeJS and Electron API

To use NodeJS and Electron API in your application, you have to add kvision-electron module to your `build.gradle.kts` dependencies. NodeJS Kotlin bindings are provided by [node-kt](https://github.com/Shengaero/node-kt) project \(package `node.*`\) and Electron bindings are provided directly by KVision module \(package `pl.treksoft.kvision.electron.*`\).

{% hint style="info" %}
Note: The KVision application works within [renderer process](https://electronjs.org/docs/glossary#renderer-process) of Electron, and you have to use [remote](https://electronjs.org/docs/api/remote) object or [ipcRenderer](https://electronjs.org/docs/api/ipc-renderer) to get access to some parts of Electron API.
{% endhint %}

