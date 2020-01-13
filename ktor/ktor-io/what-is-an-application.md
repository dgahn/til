# What is an Application?

A ktor Server Application은 **configured server engine**을 사용하는 하나 이상의 포트를 사용하는 커스텀 프로그램이다. application logic들로 module들이 구성된다. 
**routing**, **sessions**, **compression**등과 같은 기능들을 설치하는 application logic으로 모듈들이 구성된다. 

## Application

**Application** 인스턴스는 Ktor Application의 주요 unit 이다. 요청이 들어왔을 때 **ApplicationCall**로 변환되고 pipeline을 통해서 application의 소유가 된다. pipeline은 하나 이상의 인터셉터로 구성되어 있다.
그리고 routing, compression등과 같은 어떤 함수적으로 제공되는 인터셉터이다.

일반적으로 Ktor 프로그램은 Application pipeline을 모듈을 통해서 설정한다. 모듈들은 **feautres**을 설치하고 설정한다.

## Features

feature는 pipeline을 설치하고 설정할 수 있는 싱글톤이다. Ktor는 몇가지 표준 feature를 제공하고 뿐만 아니라 너 자신이나 커뮤니티로부터 다른 features를 추가할 수 있다. 어떤 pipeline으로부터 features를 설치할 수 있다.

## Modules

Ktor 모듈은 사용자 정의 함수다. 이 함수는 서바 파이프 라인 구성을 담당하는 애플리케이션 클래스다. features 설치, routes 등록, 요청 핸들링등을 한다.

서버가 시작할 때 **application.conf** 파일로부터 특별한 모듈을 가질 수 있다.

간단한 모듈 함수는 다음과 같다.

```kotlin
package com.example.myapp

fun Application.mymodule() {
    routing {
        get("/") {
            call.respondText("Hello World!")
        }
    }
}
```

물론, module 함수를 몇개의 작은 함수나 클래스로 나눌 수 있다.

modules은 그들의 qualified name으로 참조된다. 예를 들어, 다음과 같다.

```kotlin
com.example.myapp.MainKt.mymodule
```