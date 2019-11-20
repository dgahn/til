# HTTP Server Logic

메인 클래스인 **QuckstartServer**는 메인 메소드에 의해서 실행시킬 수 있다. **QuckstartServer** 클래스는 모든 것을 다 가져오는 것을 목표로 합니다. **ActorSystem**을 시작합니다. **ActorSystem**은 모든 **Actor**를 실행시키고 다른 디펜던시도 시작시키는 root가 된다.

```kotlin
class QuickstartApp {
    fun startHttpServer(
        route: Route,
        system: ActorSystem<*>
    ) {
        val classicSystem: akka.actor.ActorSystem = Adapter.toClassic(system)
        val http: Http = Http.get(classicSystem)
        val materializer: Materializer = Materializer.matFromSystem(system)
        val routeFlow: Flow<HttpRequest, HttpResponse, NotUsed> = route.flow(classicSystem, materializer)
        val futureBinding: CompletionStage<ServerBinding> =
            http.bindAndHandle(routeFlow, ConnectHttp.toHost("localhost", 8080), materializer)
        futureBinding.whenComplete { binding: ServerBinding?, exception: Throwable? ->
            if (binding != null) {
                val address: InetSocketAddress = binding.localAddress()
                system.log().info(
                    "Server online at http://{}:{}/",
                    address.hostString,
                    address.port
                )
            } else {
                system.log().error("Failed to bind HTTP endpoint, terminating system", exception)
                system.terminate()
            }
        }
    }
}

fun main() {
    val rootBehavior =
        Behaviors.setup { context: ActorContext<NotUsed?> ->
            val userRegistryActor: ActorRef<UserRegistry.Command?> =
                context.spawn(UserRegistry.create(), "UserRegistry")
            val userRoutes = UserRoutes(context.system, userRegistryActor)
            QuickstartApp().startHttpServer(userRoutes.userRoutes(), context.system)
            Behaviors.empty()
        }

    ActorSystem.create(rootBehavior, "HelloAkkaHttpServer");
}
```

**UserRoutes** 클래스를 분리했다는 것을 주목해라. 이 클래스에 실제 **Route**에 대한 정보를 넣는다.

<br>

# Binding endPoints

각 Akka HTTP **Route**는 하나 이상의  **akka.http.javadsl.server.Directive**로 구성된다(such as: path, get, post, complete, etc.). 
[low-level API](https://doc.akka.io/docs/akka-http/current/server-side/low-level-api.html?language=java)는 수동적으로 **request**와 **response**를 만드는 것에 대해서 허락한다.
이 예제는 User registry service를 위해서 다음과 같은 action들이 필요하다. 

| Functionality      | HTTP Method | Path       | Returns              |
| :----------------- | :---------- | :--------- | :------------------- |
| Create a user      | POST        | /users     | Confirmation message |
| Retrieve a user    | GET         | /users/$ID | JSON payload         |
| Remove a user      | DELETE      | /users/$ID | Confirmation message |
| Retrieve all users | GET         | /users     | JSON payload         |

우리의 애플리케이션에서 **Route**의 정의는 **UserRoutes** 클래스 안에서 분류된다. 그리고 클래스의 생성자를 통해서 **Route** 기능을 사용할 수 있다.

더 큰 애플리케이션에서는 여러개의 **Route** 클래스를 정의할 수 있다. 그런 경우에는 다음과 같이 많은 **Route**를 붙이면 된다.

- **Route route = concat(UserRoutes.userRoutes(), healthCheckRoutes, ...)**

**Route**에 대한 예시의 일부분을 보고 각 action에 대한 endpoints 바인딩, HTTP method, 그리고 메시지 또는 payload를 어떻게 처리하는지 보자.

### Retrieving and creating users

**users**를 검색하고 만드는 endpoint의 정의는 다음과 같다.

```kotlin
path(PathMatchers.segment()) {
    concat(
        get {
            rejectEmptyResponse {
                onSuccess(getUser(name)) { performed ->
                    complete(StatusCodes.OK, performed.maybeUser, Jacskon.marshaller())
                }
            }
        },
        delete {
            onSuccess(deleteUser(name)) {
                performed -> 
                    return complete(StatusCodes.OK, performed, Jackson.marshaller())
            }
        }
    )

}
```

**Route**는 들어오는 요청을 적절한 핸들러 블록으로 Route하는 다양한 지시문을 중첩하여 구성한다. 위 코드에서 블락을 만드는 directives는 다음과 같다.

#### Generic functionality

예제에서 사용되는 directives은 다음과 같다.

- **pathPrefix("users")** : 이 Path는 들어오는 요청에 대하여 일치시키는데 사용된다.
- **pathEnd** : 다른 경로가 아닌 이미 완전히 일치한 경로를 의미한다. 이 경우 "users**가 된다.
- **concat** : 두개 이상의 다른 경로를 연결한다. **Routes**는 차례로 경로를 시도한다. 만약 어느 경로에서 요청이 거절되면 연결되어 있는 다음 경로를 요청을 시도한다. 
  이것을 어느 route가 올바른 응답을 생성할 때까지 시도한다. 만약, 모든 route가 그 요청을 거절하면, 경로를 연결하는 것을 거절한다. 이런 경우 route는 좀 더 높은 수준의 경로를 찾는 것을 시도한다.
  만약 root level의 route가 거절하면 그 때 에러 응답을 요청에 대한 정보가 왜 거절되었는지에 대한 정보와 함께 응답을 보낸다.

#### Retrieving users

- **get** : **GET** HTTP 메소드에 대해 매칭이 된다.
- **compelte** : 요청에 대한 응답을 완성하여 HTTP 응답을 한다.
  
#### Creating a user

- **post** : **POST** 메소드와 매칭이 된다.
- **entity(Unmarshaller<T>, T -> Directive)** : HTTP 요청 body를 User type로 도메인 객체로 변경한다. 암시적으로, 우리는 Content Type이 **application/json**이라고 추정한다.
우리는 어떻게 이것이 [JSON](https://developer.lightbend.com/guides/akka-http-quickstart-java/json.html)으로 동작하는지 알 수 있다.
- **complete** : 요청에 대한 응답을 완성하여 HTTP 응답을 한다. 이 요청은 다른 HTTP status code를 결합 할 수 있다. 그리고 object도 marshaller을 통해서 body로 결합할 수 있다.

#### Retirieving and removing a user

다음, 이 예제는 user를 어떻게 검색하고 지우는지에 대해 정의한다. 이런 경우 **URI**는 반드시 **/user/$ID** 폼안에서 user의 id를 포함해야한다. 다음 코드에서 이를 식별할 수 있는지 확인해보자. route의 이 부분은 GET과 DELETE 메소드 모두에 포함한다.


```kotlin
path(PathMatchers.segment()) { name: String? ->
    concat(
        get {
            rejectEmptyResponse {
                onSuccess(getUser(name!!)) { performed: GetUserResponse? ->
                    complete(StatusCodes.OK, performed!!.maybeUser, Jackson.marshaller())
                }
            }
        },
        delete {
            onSuccess(deleteUser(name!!)) { performed: ActionPerformed? ->
                complete(StatusCodes.OK, performed, Jackson.marshaller())
            }
        }
    )
}
```

- **path(Segment) { name =>** : 이 코드의 조각은 **/users/$ID** 형식의 URI와 일치한다. 그리고 **Segment**는 자동적으로 URI에 있는 **\$ID** 값을 **name** 변수에 할당합니다. 예를 들어, **/users/Bruce**는 **name** 변수에 **Bruce**를 할당합니다. URI를 다루는 내용은 [pattern matchers](https://doc.akka.io/docs/akka-http/current/routing-dsl/path-matchers.html?language=scala#basic-pathmatchers)를 참고하세요.

#### Retrieving a user

들어오는 요청에 대해서 처리 하는 로직을 세분화할 수 있다.

```kotlin
rejectEmptyResponse {
    onSuccess(getUser(name!!)) { performed: GetUserResponse? ->
        complete(StatusCodes.OK, performed!!.maybeUser, Jackson.marshaller())
    }
}
```

위의 **rejectEmptyResponse**는 자동적으로 미래에 unwrap하는 편의 메소드다. **rejectEmptyResponse**는 리소스가 없는 것을 의미하는 HTTP status code 404, **ExceptionHandler**로 인한 HTTP status code 500등을 처리하는데 사용할 수 있다.

#### Deleting a user

- **delete**: Http directive인 **DELETE**에 매칭이 된다.


delete 요청은 다음과 같은 로직을 따른다.

```kotlin
delete {
    onSuccess(deleteUser(name!!)) { performed: ActionPerformed? ->
        complete(StatusCodes.OK, performed, Jackson.marshaller())
    }
}
```

그래서 우리는 user registry actor에 user를 지우는 지시를 보낼 수 있다. 그리고 응답을 받으면 적절한 HTTP status code를 client에 반환한다.

<br>

# The complete Route

다음은 sample application의 완성된 **Route** 정의이다.

```kotlin
fun userRoutes(): Route {
    return pathPrefix("users") {
        concat(
            //#users-get-delete
            pathEnd {
                concat(
                    get {
                        onSuccess(getUsers()) { users: Users? ->
                            complete(StatusCodes.OK, users, Jackson.marshaller())
                        }
                    },
                    post {
                        entity(Jackson.unmarshaller(User::class.java)) { user: User? ->
                            onSuccess(createUser(user!!)) { performed: ActionPerformed? ->
                                complete(StatusCodes.CREATED, performed, Jackson.marshaller())
                            }
                        }
                    })
            },
            path(PathMatchers.segment()) { name: String? ->
                concat(
                    get {
                        rejectEmptyResponse {
                            onSuccess(getUser(name!!)) { performed: GetUserResponse? ->
                                complete(StatusCodes.OK, performed!!.maybeUser, Jackson.marshaller())
                            }
                        }
                    },
                    delete {
                        onSuccess(deleteUser(name!!)) { performed: ActionPerformed? ->
                            complete(StatusCodes.OK, performed, Jackson.marshaller())
                        }
                    }
                )
            } //#users-get-post
        )
    }
}
```

이러한 route를 더 작은 route 값으로 분리하고 **concat**을 통해서 연결하면 분리된 연결을 허락하고 작은 라우팅 tree를 얻을 수 있는 **userRoutes**을 얻을 수 있다. 

<br>

# Binding the HTTP server

TCP port에 HTTP server를 위한 **Route**를 바인딩 하는 것은 root behavior actor가 분리된 메소드인 **startHttpServer**을 통해서 startup 되면서 된다.
이는 bootstrap actor의 내부 상태를 실수로 액세스 하는 것을 피하기 위해서다.

**bindAndhandle** 메소드는 **routes**, **host**, **port**등 3가지 파라미터를 실제로 바인딩한다. 이 바인딩은 비동기적으로 일어나므로 **bindAndhandle** 메소드는 미래에 **Future**을 반환한다. 이 **Future**는 바인딩을 나타내는 객체 또는 HTTP 라우트 바인딩에 실패한 경우 실패를 가진다.(예를 들어, 이 포트가 이미 사용되고 있는지)

실패가 일어나서 만약 바인딩 할 수 없다면 애플리케이션을 확실히 중지시키기 위해서 Actor system를 종료시킨다.

```kotlin
class QuickstartApp {
    fun startHttpServer(
        route: Route,
        system: ActorSystem<*>
    ) {
        val classicSystem: akka.actor.ActorSystem = Adapter.toClassic(system)
        val http: Http = Http.get(classicSystem)
        val materializer: Materializer = Materializer.matFromSystem(system)
        val routeFlow: Flow<HttpRequest, HttpResponse, NotUsed> = route.flow(classicSystem, materializer)
        val futureBinding: CompletionStage<ServerBinding> =
            http.bindAndHandle(routeFlow, ConnectHttp.toHost("localhost", 8080), materializer)
        futureBinding.whenComplete { binding: ServerBinding?, exception: Throwable? ->
            if (binding != null) {
                val address: InetSocketAddress = binding.localAddress()
                system.log().info(
                    "Server online at http://{}:{}/",
                    address.hostString,
                    address.port
                )
            } else {
                system.log().error("Failed to bind HTTP endpoint, terminating system", exception)
                system.terminate()
            }
        }
    }
}

fun main() {
    val rootBehavior =
        Behaviors.setup { context: ActorContext<NotUsed?> ->
            val userRegistryActor: ActorRef<UserRegistry.Command?> =
                context.spawn(UserRegistry.create(), "UserRegistry")
            val userRoutes = UserRoutes(context.system, userRegistryActor)
            QuickstartApp().startHttpServer(userRoutes.userRoutes(), context.system)
            Behaviors.empty()
        }

    ActorSystem.create(rootBehavior, "HelloAkkaHttpServer");
}
```

**QuickstartApp.kt**에서 root behavior setup하면서 사용할 다양한 actor들을 묶을 수 있음 코드에서 볼 수 있다. 
user registry actor를 보고 **Terminated** 메시지를 다루지 않으면 root behavior가 멈추거나 훼손될 경우 Actorsystem 자체가 중지된다.