# Setting up

KVision supports three server-side frameworks - Ktor, Jooby and Spring Boot - so you have to choose one of them for your needs. It's worth to mention, that common and client modules of your application are exactly the same for all three servers, as well as the actual service implementation in the server module. The differences are tied to the actual framework build configuration and initialization code.

KVision fullstack applications utilize [Kotlin multiplatform](https://kotlinlang.org/docs/reference/multiplatform.html) architecture.

