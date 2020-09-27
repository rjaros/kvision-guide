# Setting up

KVision supports six server-side frameworks - Ktor, Jooby, Spring Boot, Javalin, Vert.x and Micronaut - so you have to choose one of them for your needs. It's worth to mention, that common and frontend targets of your application are exactly the same for all three servers, as well as the greater part of the actual service implementation in the backend target. The differences are tied to the actual framework build configuration and initialization code.

KVision fullstack applications utilize [Kotlin multiplatform](https://kotlinlang.org/docs/reference/multiplatform.html) architecture \(Kotlin 1.3 MPP\). That's why you have to prepare the special Gradle configuration and the project layout. To start, it's best to just clone one of the template-fullstack projects from [kvision-examples](https://github.com/rjaros/kvision-examples) GitHub repository.

The application sources are split into three source sets - `common`, `frontend` and `backend`, located in three directories: `src/commonMain` `src/frontendMain` and `src/backendMain`. The requirements and dependencies for the build process are the same as mentioned in [Part 1 of this guide](../part-1-fundamentals/setting-up.md).

Since version 2.0 you can use KVision compiler plugin to generate code in the common and frontend modules. You can still create this code by hand, but it's definitely easier to use the plugin.  

## Development

During the development phase you compile and run frontend and backend targets separately.

#### Frontend

To run the frontend application with Gradle continuous build enter:

```text
./gradlew -t frontendRun                                (on Linux)
gradlew.bat -t frontendRun                              (on Windows)
```

#### Backend

To run the backend application enter:

```text
./gradlew backendRun                                    (on Linux)
gradlew.bat backendRun                                  (on Windows)
```

There are different levels of support when it comes to auto-reload. Javalin doesn't support auto-reload at all. Jooby and Vert.x have built-in auto-reload based on sources monitoring, so it works out of the box. In case of Ktor and Spring Boot auto-reload is based on the classpath monitoring, so you have to run another Gradle process for continuous build:

```text
### Ktor or Spring Boot
./gradlew -t compileKotlinBackend                       (on Linux)
gradlew.bat -t compileKotlinBackend                     (on Windows)
```

{% hint style="info" %}
Jooby's Gradle plugin has some issues with Kotlin multiplatform configuration and auto-reload works only when backend sources are put into `java` directory \(not `kotlin`\).
{% endhint %}

After both parts of your application are running, you can open [http://localhost:3000/](http://localhost:3000/) in your favorite browser. Changes made to your sources \(in any source set\) should be automatically applied to your running application. 

{% hint style="info" %}
You have to compile applications with Gradle, to allow KVision compiler plugin to generate some common and frontend code. You should run`compileKotlinMetadata` Gradle task at least once, to allow project compilation in IntelliJ.
{% endhint %}

## Production

To build complete application optimized for production run:

```text
./gradlew clean jar                   (on Linux)
gradlew.bat clean jar                 (on Windows)
```

The application jar will be saved in `build/libs` directory \(`projectname-1.0.0-SNAPSHOT.jar`\). You can run your application with  the`java -jar` command.

```text
java -jar build/libs/projectname-1.0.0-SNAPSHOT.jar
```

