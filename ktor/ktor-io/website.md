# Guides: How to create a plain website using ktor

이 문서에서는 ktor를 이용해서 어떻게 HTML 웹사이트를 만들지는지에 대해서 배운다. 사용자를 위한 백엔드, 로그인 폼 그리고 지속적인 세션을 간단하게 만든다. 
위의 요구사항을 만족하기 위해서 **Routing**, **StatusPages**, **Authentication**, **Sessions**, **StaticContent**, **FreeMarker** 그리고 **HTML DSL**등의 기술을 사용한다.

## Setting up the project

프로젝트의 생성은 [quck-start](./quick-start.md)를 참고한다.

## Simple routing

간단한 라우팅을 해보자. 다음은 Ktor의 핵심 부분이다. 다른 추가적인 부분은 필요 없다. 
이 기능은 **rouing { }** 을 통해서 자동적으로 수행된다. 다음은 간단하게 GET route를 통해 **OK**를 응답 받는 코드이다.

```kotlin
import io.ktor.application.Application
import io.ktor.application.call
import io.ktor.response.respondText
import io.ktor.routing.get
import io.ktor.routing.routing
import io.ktor.server.engine.embeddedServer
import io.ktor.server.netty.Netty

fun main() {
    embeddedServer(Netty, 8080){ module() }.start(wait = true)
}

fun Application.module() {
    routing {
        get("/") {
            call.respondText("OK")
        }
    }
}
```

**embeddedServer**를 실행할 때, **Application**의 어떤 기능 함수를 넣을지 넣어줘야 한다. 라우팅 기능을 넣기 위해서 **Application.module()**을 추가한다.

## Serving HTML with FreeMarker

Apache FreeMarker는 JVM을 위한 템플릿 엔진이다. Ktor는 FreeMarker의 기능을 제공한다.
templates 폴더에 리소스 부분에 템플릿을 저장한다. **resources/templates/index.ftl**을 만들고 다음의 코드를 작성하자.

```html
<html>
	<body>
		<ul>
		<#list data.items as item>
			<li>${item}</li>
		</#list>
		</ul>
	</body>
</html>
```

**FreeMarker** 기능을 사용할 수 있도록 다음의 의존성을 추가한다.

```kotlin
implementation "io.ktor:ktor-freemarker:$ktor_version"
```

라우팅 코드를 수정한다.


```kotlin
data class IndexData(val items: List<Int>)

fun Application.module() {
    install(FreeMarker) {
        templateLoader = ClassTemplateLoader(this::class.java.classLoader, "templates")
    }
    
    routing {
        get("/html-freemarker") {
            call.respond(FreeMarkerContent("index.ftl", mapOf("data" to IndexData(listOf(1, 2, 3))), ""))
        }
    }
}
```

**http://127.0.0.1:8080/html-freemarker**의 결과물은 다음과 같다.

<img src="https://imgur.com/w7yDJzf.png" width=400>

## Serving static files: styles, scripts, images...

템플릿에 추가적으로 정적 컨텐츠를 넣고 넣을 수 있다. 정적 컨텐츠는 빠르게 서비스할 수 있고 파일을 다운로드 하는 것 같은 다른 컨텐츠 기능과 호환 가능하다.

간단한 **styles.css** 파일을 페이지에 적용하자.

정적 파일 기능을 제공하는데 별다른 설정은 필요없다. 하지만 Route Handler는 설정해야한다. 

```kotlin
routing {
    // ...
    static("/static") {
        resources("static")
    }
}
```

정적 파일을 제공하기 위해서 **/static** url을 **/resources/static**을 설정하자.

```css
body {
    background: #B9D8FF;
}
```

그리고 **index.ftl** 파일에 다음과 같이 코드를 수정하자.

```html
<#-- @ftlvariable name="data" type="com.example.IndexData" -->
<html>
    <head>
        <link rel="stylesheet" href="/static/styles.css">
    </head>
    <body>
	<!-- ... -->
    </body>
</html>
```

결과는 다음과 같다.

<img src="https://imgur.com/hWICtFV.png" width=400>

## Enabling partial content: large file and videos

지금은 필요 없지만 큰 정적파일을 사용하는 것에 대해서는 partical content를 지원한다.

```kotlin
install(PartialContent) {

}
```

## Creating a form

가짜 로그인 폼을 만들어보자. **resources/templates/login.ftl**을 추가하자.

```html
<html>
<head>
    <link rel="stylesheet" href="/static/styles.css">
</head>
<body>
<#if error??>
    <p style="color:red;">${error}</p>
</#if>
<form action="/login" method="post" enctype="application/x-www-form-urlencoded">
    <div>User:</div>
    <div><input type="text" name="username" /></div>
    <div>Password:</div>
    <div><input type="password" name="password" /></div>
    <div><input type="submit" value="Login" /></div>
</form>
</body>
</html>
```

템플릿을 추가하고 몇가지 로직을 추가해야한다. 새로운 라우트를 추가하자.


```kotlin
route("/login") {
    get {
        call.respond(FreeMarkerContent("login.ftl", null))
    }
    post {
        val post = call.receiveParameters()
        if (post["username"] != null && post["username"] == post["password"]) {
            call.respondText("OK")
        } else {
            call.respond(FreeMarkerContent("login.ftl", mapOf("error" to "Invalid login")))
        }
    }
}
```

**username**과 **password**는 같게 했다. 하지만 **null** 값은 받지 않는다. 로그인 유효하면 **OK**를 응답하고 실패하면 **error**를 보여준다.

## Redirections

route를 리팩토링하거나 폼을 다시 받는 경우에는 redirection을 수행해야한다. 지금은 성공적으로 로그인 한 경우에 홈페이지를 리다렉트 한다.

```kotlin
call.respondRedirect("/", permanent=false)
```

## Using the Form authentication

POST의 파라미터를 어떻게 받는지를 설명하기 위해 로그인을 수동적으로 핸들링 해봐야한다. 하지만 인증 기능을 **form**으로 제공한다.

우선 의존성을 추가해야한다.

```kotlin
implementation("io.ktor:ktor-auth:$ktorVersion")
```

코드를 작성해보자.

```kotlin
install(Authentication) {
    form("login") {
        userParamName = "username"
        passwordParamName = "password"
        challenge = FormAuthChallenge.Unauthorized
        validate { credentials -> if (credentials.name == credentials.password) UserIdPrincipal(credentials.name) else null }
    }
}
route("/login") {
    get {
        // ...
    }
    authenticate("login") {
        post {
            val principal = call.principal<UserIdPrincipal>()
            call.respondRedirect("/", permanent = false)
        }
    }
}
```

## Sessions

모든 페이지에 인증을 가지기 위해서 세션에 유저를 저장하자. 그리고 세션은 세션 쿠키를 사용하는 모든 페이지에 전파된다.

코드를 다음과 같이 수정하자.

```kotlin
data class MySession(val username: String)

fun Application.module() {
    install(Sessions) {
        cookie<MySession>("SESSION")
    }

    route("/login") {
            get {
                call.respond(FreeMarkerContent("login.ftl", null))
            }

            authenticate("login") {
                post {
                    val principal = call.principal<UserIdPrincipal>() ?: error("No principal")
                    call.sessions.set("SESSION", MySession(principal.name))
                    call.respondRedirect("/", permanent = false)
                }
            }
        }
} 
```

페이지에서 세션을 가져올 수 있고 로그인에 대해서 다른 결과를 낼 수 있다.

```kotlin
fun Application.module() {
    // ...
    get("/") {
            val session: MySession? = call.sessions.get()
            if (session != null) {
                call.respondText("User is logged")
            } else {
                call.respond(FreeMarkerContent("index.ftl", mapOf("data" to IndexData(listOf(1, 2, 3))), ""))
            }
        }
}
```

## Using HTML DSL instead of FreeMarker

Template Engine을 사용하는 것 대신에 HTML 코드를 선택할 수 있다. HTML DSL를 사용할 수 있다. HTMl DSL은 별도의 설정을 필요로 하지 않고 다음과 같이 작성하면 된다.

```kotlin
get("/") { 
    val data = IndexData(listOf(1, 2, 3))
    call.respondHtml {
        head {
            link(rel = "stylesheet", href = "/static/styles.css")
        }
        body {
            ul {
                for (item in data.items) {
                    li { +"$item" }                
                }
            }
        }
    }
}
```

HTML DSL의 주요 이점은 변수에 정적인 타입으로 접근할 수 있다. 그리고 코드를 통해서 HTML을 작성할 수 있다.
