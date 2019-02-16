# Setting up

KVision supports three server-side frameworks - Ktor, Jooby and Spring Boot - so you have to choose one of them for your needs. It's worth to mention, that common and client modules of your application are exactly the same for all three servers, as well as the greater part of the actual service implementation in the server module. The differences are tied to the actual framework build configuration and initialization code.

KVision full-stack applications utilize [Kotlin multiplatform](https://kotlinlang.org/docs/reference/multiplatform.html) architecture. That's why you have to prepare the special Gradle configuration and the project layout. To start, it's best to just clone one of the template-fullstack projects from [kvision-examples](https://github.com/rjaros/kvision-examples) GitHub repository.

{% hint style="info" %}
Note: The "new MPP" model introduced in Kotlin 1.3 is not yet supported, because of problems with various Gradle plugins.
{% endhint %}

The application project is split into three Gradle subprojects - `common`, `client` and `server`. Every subproject has it's own `build.gradle` configuration file and it's own sources \(`src` folder\). The requirements and dependencies for build process are the same as mentioned in [Part 1 of this guide](../part-1-fundamentals/setting-up.md).

## Development

During the development phase you compile and run client and server modules separately.

#### Client

To run the client application with Gradle continuous build enter:

```text
./gradlew -t client:run                                    (on Linux)
gradlew.bat -t client:run                                  (on Windows)
```

#### Server

To run the server application you should use the command depending on your framework choice.

```text
### Ktor
./gradlew server:run                                    (on Linux)
gradlew.bat server:run                                  (on Windows)

### Jooby
./gradlew server:joobyRun                               (on Linux)
gradlew.bat server:joobyRun                             (on Windows)

### Spring Boot
./gradlew server:bootRun                               (on Linux)
gradlew.bat server:bootRun                             (on Windows)
```

All three frameworks have auto-reload feature, but only Jooby is watching for changes directly in the source code. In case of Ktor and Spring Boot auto-reload is based on the classpath monitoring, so you have to run another Gradle process for continuous build \(in the additional terminal/console window\).

```text
### Ktor or Spring Boot
./gradlew -t server:build                                    (on Linux)
gradlew.bat -t server:build                                  (on Windows)
```

After both parts of your application are running, you can open [http://localhost:8088/](http://localhost:8088/) in your favorite browser. Changes made to your sources \(in any module\) should be automatically applied to your running application. 

## Production

To build complete application optimized for production you run the command depending on your framework choice.

```text
### Ktor or Jooby
./gradlew -Pprod=true clean shadowJar                   (on Linux)
gradlew.bat -Pprod=true clean shadowJar                 (on Windows)

### Spring Boot
./gradlew -Pprod=true clean bootJar                     (on Linux)
gradlew.bat -Pprod=true clean bootJar                   (on Windows)
```

The application jar will be saved in `server/build/libs` directory \(`server-all.jar` or `server.jar` respectively\). You can run your application with  the`java -jar` command.

```text
### Ktor or Jooby
java -jar server/build/libs/server-all.jar

### Spring Boot
java -jar server/build/libs/server.jar
```

