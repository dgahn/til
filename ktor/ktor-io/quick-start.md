# Quick Start

Ktor는 웹 애플리케이션, HTTP 서비스, 모바일 그리고 브라우저 애플리케이션에 연결할 애플리케이션을 쉽게 빌드 할 수 있다. 현대 연결된 애플리케이션은 비동기성을 제공하고 코틀린의 코루틴을 쉽게 제공해야한다. 

아직 완벽하지는 않지만, Ktor의 목표는 end-to-end의 멀티플랫폼 애플리케이션 프레임을 제공한다. 현재 JVM 클라이언트-서버 시나리오는 JavaScript, IOS 그리고 안드로이드 클라이언트를 제공하고 있다. 그리고 Ktor는 서버 환경을 기본 환경으로 클라이언트 기능을 다른 기본 대상으로 가져오기 위해 노력하고 있다.

## Set up a Gradle Build

Gradle을 통해서 Ktor 설정하는 방법에 대해서 기술하도록 하겠다.

### 1) 먼저, 다음과 같이 설정을 선택합니다.
<img src="https://imgur.com/JRWE0Rc.png" width=800>

### 2) 다음으로 프로젝트의 artifacts를 선택합니다.
<img src="https://imgur.com/gJc06Iy.png" width=800>

### 3) 프로젝트가 생성되면 **build.gradle.kts**을 수정합니다.

```kotlin
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    java
    kotlin("jvm") version "1.3.61"
    application
}

repositories {
    mavenCentral()
}

dependencies {

    val ktorVersion = "1.2.6"

    implementation(kotlin("stdlib-jdk8"))
    implementation("io.ktor:ktor-server-netty:$ktorVersion")
    testImplementation("io.kotlintest:kotlintest-runner-junit5:3.3.0")
}

configure<JavaPluginConvention> {
    sourceCompatibility = JavaVersion.VERSION_11
}

application {
    applicationName = "hello-ktor"
    group = "me.dgahn"
    version = "1.0.0"
    mainClassName = "me.dgahn.hk.ApplicationInitializerKt"
}

tasks.withType<Test> {
    useJUnitPlatform()
}

tasks.withType<KotlinCompile> {
    kotlinOptions.jvmTarget = "11"
}
```

### 4) Main 클래스를 작성하고 프로그램을 실행합니다.

```kotlin
package me.dgahn.hk

import io.ktor.application.call
import io.ktor.http.ContentType
import io.ktor.response.respondText
import io.ktor.routing.get
import io.ktor.routing.routing
import io.ktor.server.engine.embeddedServer
import io.ktor.server.netty.Netty

fun main() {
    embeddedServer(Netty, 8080) {
        routing {
            get("/") {
              call.respondText("My Example Blog", ContentType.Text.Html)
            }
        }
    }.start(wait = true)
}
```