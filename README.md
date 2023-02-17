---
description: This is a guide for KVision - an object oriented web framework for Kotlin/JS.
---

# KVision Guide

### fun Application.main() { install(Compression) install(DefaultHeaders) install(CallLogging) install(Sessions) { cookie("KTSESSION", storage = SessionStorageMemory()) { cookie.path = "/" cookie.extensions\["SameSite"] = "strict" } } Db.init(environment.config)

```
install(Authentication) {
    form {
        userParamName = "username"
        passwordParamName = "password"
        validate { credentials ->
            dbQuery {
                UserDao.select {
                    (UserDao.username eq credentials.name) and (UserDao.password eq DigestUtils.sha256Hex(
                        credentials.password
                    ))
                }.firstOrNull()?.let {
                    UserIdPrincipal(credentials.name)
                }
            }
        }
        skipWhen { call -> call.sessions.get<Profile>() != null }
    }
}

routing {
    applyRoutes(getServiceManager<IRegisterProfileService>())
    authenticate {
        post("login") {
            val principal = call.principal<UserIdPrincipal>()
            val result = if (principal != null) {
                dbQuery {
                    UserDao.select { UserDao.username eq principal.name }.firstOrNull()?.let {
                        val profile =
                            Profile(it[UserDao.id], it[UserDao.name], it[UserDao.username].toString(), null, null)
                        call.sessions.set(profile)
                        HttpStatusCode.OK
                    } ?: HttpStatusCode.Unauthorized
                }
            } else {
                HttpStatusCode.Unauthorized
            }
            call.respond(result)
        }
        get("logout") {
            call.sessions.clear<Profile>()
            call.respondRedirect("/")
        }
        applyRoutes(getServiceManager<IAddressService>())
        applyRoutes(getServiceManager<IProfileService>())
    }
}
kvisionInit()
```

}[KVision Guide](https://kvision.gitbook.io/kvision-guide/)

Current version: 6.2.0

<img src=".gitbook/assets/logo4.svg.png" alt="" data-size="original">&#x20;

This guide will walk you through creating applications using KVision, whether for the web, mobile, or desktop. Familiarity with Kotlin (or at least Java) will help you be successful faster. You do not need a thorough understanding of Javascript to build great looking web applications, though as you get deeper into some of the browser technologies some knowledge of Javascript's many interesting quirks and features will be beneficial.

KVision is built on the great work being done by JetBrains on KotlinJS, which allows Kotlin to target Javascript when being compiled. JetBrains have created a great foundation (which they continue to improve); KVision aims to provide everything else you'll need to take your ideas to production.
