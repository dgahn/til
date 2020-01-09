# HTTP Api

이 가이드는 Ktor를 통해서 어떻게 API를 만드는지에 대해서 설명한다. 텍스트 스니펫을 저장하는 간단한 API를 만들어보자.
이를 하기 위해서는 **Routing**, **StatusPages**, **Authentication**, **JWT Authentication**, **CORS**, **ContentNegotiation** 그리고 **Jackson**등의 기능이 필요하다.

## Simple routing

간단한 라우팅을 만들자.

```kotlin
fun Application.module() {
    routing {
        get("/snippets") {
            call.respondText("OK")
        }
    }
}
```

## Serving JSON content

HTTP API는 주로 JSON 형식으로 응답한다. Content Type에 대한 설정을 Jackson으로 하자. (참고 : [Content Neogotiation](https://ktor.io/servers/features/content-negotiation.html))

```kotlin
fun Application.module() {
    install(ContentNegotiation) {
        jackson {
        }
    }
    routing {
        // ...
    }
}
```

요청에 대한 응답을 JSON으로 하기 위해서는 **call.respond**를 사용한다.

```kotlin
routing {
    get("/snippets") {
        call.respond(mapOf("OK" to true))
    }
}
```

## Handling other HTTP methods

HTTP API는 기능을 위해 대부분의 HTTP methods/verbs를 사용한다. 새로운 snippets를 만들 route를 추가하자. POST 요청에 대한 JSON body를 읽을 수 있다.

```kotlin
data class PostSnippet(val snippet: PostSnippet.Text) {
    data class Text(val text: String)
}

// ...

routing {
    get("/snippets") {
        call.respond(mapOf("snippets" to synchronized(snippets) { snippets.toList() }))
    }
    post("/snippets") {
        val post = call.receive<PostSnippet>()
        snippets += Snippet(post.snippet.text)
        call.respond(mapOf("OK" to true))
    }
}
```

## Grouping routes together

두가지 분리되어 있는 routes를 path에 공유하고 있다. path 자체는 중복될 필요가 없다. route path 없이 오버로드하는 방법은 다음과 같다.

```kotlin
routing {
    route("/snippets") {
        get {
            call.respond(mapOf("snippets" to synchronized(snippets) { snippets.toList() }))
        }
        post {
            val post = call.receive<PostSnippet>()
            snippets += Snippet(post.snippet.text)
            call.respond(mapOf("OK" to true))
        }
    }
}
```

## Authentication

snippet을 posting 하는데 모든 사람에게 예방하는 것은 좋은 생각이다. http에 대한 기본적인 인증을 추가해보자.

```kotlin
fun Application.module() {
    install(Authentication) {
        basic {
            realm = "myrealm" 
            validate { if (it.name == "user" && it.password == "password") UserIdPrincipal("user") else null }
        }
    }
    // ...
}
```

## JWT Authentication

고정된 Authentication 대신에, JWT tokens를 사용해보자.

login-register route를 추가하자. route는 사용자 존재하지 않으면 추가하고 유효한 로그인이나 사용자 등록에 대해서 JWT token을 반환한다.
JWT token은 사용자의 이름을 포함시킨다. 그리고 포스팅을 하면 스니펫이 사용자와 연결된다. 

JWT 설정을 하자.

가장 먼저 의존성을 추가하자.

```kotlin
implementation("io.ktor:ktor-auth-jwt:$ktorVersion")
```

다음 JWT 클래스를 설정하자.

```kotlin
open class SimpleJWT(val secret: String) {
    private val algorithm = Algorithm.HMAC256(secret)
    val verifier = JWT.require(algorithm).build()
    fun sign(name: String): String = JWT.create().withClaim("name", name).sign(algorithm)
}

fun Application.module() {
    val simpleJwt = SimpleJWT("my-super-secret-for-jwt")
    install(Authentication) {
        jwt {
            verifier(simpleJwt.verifier)
            validate {
                UserIdPrincipal(it.payload.getClaim("name").asString())
            }
        }
    }
    // ...
}
```

사용자 이름과 비밀번호는 data source에 저장되야 한다. 필수적인 옵션은 다음과 같다.

```kotlin
class User(val name: String, val password: String) 

val users = Collections.synchronizedMap(
    listOf(User("test", "test"))
    .associatedBy { it.name }
    .toMutableMap()
)

class LoginRegister(val user: String, val password: String)
```

이와 같이 우리는 user를 등록하고 로깅하기 위한 route를 만들 수 있다.

```kotlin
routing {
    post("/login-register") {
        val post = call.receive<LoginRegister>()
        val user = users.getOrPut(post.user) { User(post.user, post.password) }
        if (user.password != post.password) error("Invalid credentials")
        call.respond(mapOf("token" to simpleJwt.sign(user.name)))
    }
}
```

사용자가 JWT token을 얻기 위한 준비는 완료되었다. 

## Associating users to snippets

인증된 route와 snippets를 포스팅을 하고 사용자 이름을 포함한 **Principal**을 접근하자. 그래서 snippet과 사용자를 연결해보자.

먼저 snippet에 사용자 정보를 추가하자.

```kotlin
data class Snippet(val user: String, val text: String)

val snippets = Collections.synchronizedList(mutableListOf(
    Snippet(user = "test", text = "hello"),
    Snippet(user = "test", text = "world")
))
```

이제 새로운 snippets를 저장할 때 principal 정보를 사용할 수 있다.

```kotlin
routing {
    // ...
    route("/snippets") {
        // ...
        authenticate {
            post {
                val post = call.receive<PostSnippet>()
                val principal = call.principal<UserIdPrincipal>() ?: error("No principal")
                snippets += Snippet(principal.name, post.snippet.text)
                call.respond(mapOf("OK" to true))
            }
        }
    }
}
```

## StatusPages

코드를 좀 고쳐보자. 한개의 HTTP API는 에러에 대한 Semantic 정보들을 HTTP Status code들로 제공해야한다. 지금, 예외가 발생하면 500 에러가 반환된다.
StatusPages 기능을 사용하면 특정 예외를 캡처하고 결과를 생성할 수 있다.

새로운 예외 타입을 만들어 보자.

```kotlin
class InvalidCredentialsException(message: String) : RuntimeException(message)
```

StatusPages 기능을 설치하고 exception type을 등록하고 인증되지 않는 페이지를 생성하자.

```kotlin
fun Application.module() {
    install(StatusPages) {
        exception<InvalidCredentialsException> {
            exception -> call.respond(HttpStatusCode.Unauthorized, mapOf("OK" to false, "error" to (exception.message ?: "")))
        }
    }
}
```

login-register 페이지에서 예외가 발생했을 때를 업데이트 하자.

```kotlin
routing {
    post("/login-register") {
        val post = call.receive<LoginRegister>()
        val user = users.getOrPut(post.user) {
            User(post.user, post.password)
        }
        if (user.password != post.password) throw InvalidCredentialsException("Invalid credentials")
        call.respond(mapOf("token" to simpleJwt.sign(user.name)))
    }
}
```

다른 도메인으로부터 API의 접근이 필요하다고 가정하자. CORS 설정이 필요하고 Ktor는 다음과 같이 설정한다.

```kotlin
fun Application.module() {
    install(CORS) {
        method(HttpMethod.Options)
        method(HttpMethod.Get)
        method(HttpMethod.Post)
        method(HttpMethod.Put)
        method(HttpMethod.Delete)
        method(HttpMethod.Patch)
        header(HttpHeaders.Authorization)
        allowCredentials = true
        anyHost()
    }
    // ...
}
```


전체 코드는 다음과 같다.

```kotlin
package com.example

import com.auth0.jwt.*
import com.auth0.jwt.algorithms.*
import com.fasterxml.jackson.databind.*
import io.ktor.application.*
import io.ktor.auth.*
import io.ktor.auth.jwt.*
import io.ktor.features.*
import io.ktor.http.*
import io.ktor.jackson.*
import io.ktor.request.*
import io.ktor.response.*
import io.ktor.routing.*
import java.util.*

fun main(args: Array<String>): Unit = io.ktor.server.netty.EngineMain.main(args)

fun Application.module() {
    val simpleJwt = SimpleJWT("my-super-secret-for-jwt")
    install(CORS) {
        method(HttpMethod.Options)
        method(HttpMethod.Get)
        method(HttpMethod.Post)
        method(HttpMethod.Put)
        method(HttpMethod.Delete)
        method(HttpMethod.Patch)
        header(HttpHeaders.Authorization)
        allowCredentials = true
        anyHost()
    }
    install(StatusPages) {
        exception<InvalidCredentialsException> { exception ->
            call.respond(HttpStatusCode.Unauthorized, mapOf("OK" to false, "error" to (exception.message ?: "")))
        }
    }
    install(Authentication) {
        jwt {
            verifier(simpleJwt.verifier)
            validate {
                UserIdPrincipal(it.payload.getClaim("name").asString())
            }
        }
    }
    install(ContentNegotiation) {
        jackson {
            enable(SerializationFeature.INDENT_OUTPUT) // Pretty Prints the JSON
        }
    }
    routing {
        post("/login-register") {
            val post = call.receive<LoginRegister>()
            val user = users.getOrPut(post.user) { User(post.user, post.password) }
            if (user.password != post.password) throw InvalidCredentialsException("Invalid credentials")
            call.respond(mapOf("token" to simpleJwt.sign(user.name)))
        }
        route("/snippets") {
            get {
                call.respond(mapOf("snippets" to synchronized(snippets) { snippets.toList() }))
            }
            authenticate {
                post {
                    val post = call.receive<PostSnippet>()
                    val principal = call.principal<UserIdPrincipal>() ?: error("No principal")
                    snippets += Snippet(principal.name, post.snippet.text)
                    call.respond(mapOf("OK" to true))
                }
            }
        }
    }
}

data class PostSnippet(val snippet: PostSnippet.Text) {
    data class Text(val text: String)
}

data class Snippet(val user: String, val text: String)

val snippets = Collections.synchronizedList(mutableListOf(
    Snippet(user = "test", text = "hello"),
    Snippet(user = "test", text = "world")
))

open class SimpleJWT(val secret: String) {
    private val algorithm = Algorithm.HMAC256(secret)
    val verifier = JWT.require(algorithm).build()
    fun sign(name: String): String = JWT.create().withClaim("name", name).sign(algorithm)
}

class User(val name: String, val password: String)

val users = Collections.synchronizedMap(
    listOf(User("test", "test"))
        .associateBy { it.name }
        .toMutableMap()
)

class InvalidCredentialsException(message: String) : RuntimeException(message)

class LoginRegister(val user: String, val password: String)
```
