# Server Applications

## Application and ApplicationEnvironment

실행중인 ktor Application은 **Application** class로 표현된다. ktor Application은 **modules**의 집합으로 구성되어 있다.
각 모듈은 표준 코틀린 람다 또는 함수이다. (주로 Application은 수신 또는 파라미터로 구성되어 있다.)

하나의 application은 application config를 가지는 **ApplicationEnvironment**로 표현되는 환경의 내부에서 시작한다. 

ktor server는 하나의 환경에서 시작하고 application의 생명주기를 컨트롤 한다. 하나의 application 인스턴스는 환경에 따라 만들어지고 소멸한다.
그래서 Application이 멈추는 것이 서버가 멈추는 것을 의미하지 않는다. 서버가 돌고 있으면 Application을 다시 불러올 수 있다.

Application modules은 application이 시작할 때 하나씩 시작한다. 그리고 모든 모듈은 application의 인스턴스에서 설정할 수 있다. 
application 인스턴스는 **features**을 설치하거나 **pipelines**를 인터셉팅함으로서 설정할 수 있다.

## Features

feature는 Application에 플러그하는 특별한 함수적 부분이다. 그것은 주로 요청과 응답을 인터셉트한다. 그리고 부분적인 기능들을 수행한다. 예를 들어,
**Default Headers** 기능들은 응답을 인터셉터하고 Date와 Server의 헤드들을 더한다. Feature는 application에 install 함수에 의해 다음과 같이 사용할 수 있다.

```kotlin
application.install(DefaultHeaders) {
    // configure feature
}
```

몇가지 feature들은 람다가 선택적이다. 이와 같은 경우 feature는 한번 install하면 된다. 그러나 여러 설정이 필요한 경우가 있다. 예를 들어, routing { }이 있다.

## Calls and pipelines

ktor로 들어오느 요청과 응답은 **ApplicationCall**이라고 부른다. 모든 Application call은 여러 인터셉터로 구성된 **ApplicationCallPipeline**을 통과한다.
인터셉터들은 모든 인터셉터를 하나하나 통과한다. 그 인터셉터는 다음 인터셉터로 진행하거나 전체 파이프 라인 실행을 마무리 또는 모두 찾기를 하여 요청 또는 응답을 수정하고 파이프 라인 실행을 제어할 수 있다. 
또한 continue() 호출 전후에 추가 조치를 수행하는 나머지 인터셉터 체인을 장식할 수 있다.

생각되는 예제는 다음과 같다.

```kotlin
intercept {
    myPrepare()
    try {
        proceed()
    } finally {
        myComplete()
    }
}
```

파이프라인은 몇개의 phases로 구성이 되어 있다. 모든 인터셉터는 부분적인 phase로 등록된다. 그래서 인터셉터들은 그들의 phases order로 실행될 수 있다. 

## Application call

appication call은 파라미터 셋과 요청과 응답을 쌍으로 구성한다. 그래서 application call pipeline은 receive와 send pipelines로 구성된다.
요청의 컨텐트는 ApplicationCall.receive<T>()를 사용하여 요청의 내용을 받는다. 여기서 **T**는 컨텐트의 타입을 나타낸다. 예를 들어, call.receive<String>()은 **String**으로 구성된 요청 body를 받는다.
몇가지 타입은 추가적인 설정 없이 받는다. 반면에 custom type을 받는 것은 feature installation 또는 Configuration이 필요하다. 모든 **receive()**는 receive pipeline의 원이 된다. 모든 receive pipeline 인터셉터가 요청 본문을 변환하거나 우회할 수 있도록 처음부터 실행해야한다. 오리지널 body 오브젝트는 **ByteReadChannel** 타입을 가진다.

application 응답 body는 **ApplicationCall.respond(Any)** 함수 발동에 의해 제공된다. 그 발동은 pipline 응답에 대한 실행이다. receive pipline과 유사하게, 모든 응답 pipline interceptor는 reponse object를 변형한다. 마지막으로,  응답 object는 **OutgoingContent**의 인스턴스를 변환할 수 있다.

확장된 함수 **respondText**, **respondBytes**, **receiveText**, **receiveParameters**의 한 세트를 따라서 요청 및 응답 오브젝트의 구성을 단순화한다.

## Routing

빈 application은 인턴셉터를 가지고 있지 않다. 그래서 모든 요청에 대해서 404 Not Found를 발생시킨다. application call pipline은 요청을 핸들링하기 위해 인터셉트 할것이다. 인터셉터는 request URI에 의존하여 응답할 수 있다. 예를 들어서 이와 같다.

```kotlin
intercept {
    val uri = call.request.uri
    when {
        uri = "/" -> call.respondText("Hello, World!")
        uri.startsWith("/profile/") -> { ToDo("...") }
    }
}
```

물론 이 접근은 많은 손해를 가지고 있다. 운좋게도 **Routing** 기능이 있다. 이 기능은 요청에 대해 핸들링하는 구조된 요청으로 되어 있다. 이 요청은 응용 프로그램 호출 파이프 라인을 가로 채고 경로에 대한 처리기를 등록하는 방법을 제공하는 구조적 요청 처리다. 라우팅이 하는 유일한 일은 응용프로그램 호출 파이프 라인을 가로채는 것이다. Routing은 핸들러와 인터셉터를 가진 routes의 트리로 구성된다. ktor에서 확장된 함수들의 세트로 핸들러를 이와 같이 쉽게 제공할 수 있다.

```kotlin
routing {
    get("/") {
        call.respondText("Hello, World!")
    }
    get("/profile/{id}") { ToDo("...") }
}
```

routes는 트리 구조를 가질 수 있다.

```kotlin
routing {
    route("profile/{id}") {
        get("view") { ToDo("...") }
        get("settings") { ToDo("...") }
    }
}
```

routing path는 일관적인 parts과 parameters를 포함한다. **{id}** 와 같은 예제이다. property는 **call.parameters**의 캡처된 세팅 밸류들을 제공한다.

## Content Negotiation

**ContentNegotiation**은 mime type에 대한 호환 방법을 제공하고 **Accept** 그리고 **Content-Type** 헤더들을 변환한다. content converter는 특별한 content type이나 수신하거나 응답하는 객체에 대한 타입을 등록할 수 있다. **Jackson**, **Gson** 그리고 **kotlinx.serialzation** content converters가 있다.

```kotlin
install(ContentNegotiation) {
    gson {
        // Configure Gson here
    }
}
routing {
    get("/") {
        call.respond(MyData("Hello, World!"))
    }
}
```