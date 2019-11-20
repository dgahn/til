# Backend Actor Logic

이 예제에서는 단 하나의 기본 Actor를 사용한다. 실제 시스템에서는 서로 다른 액터들이 많은 데이터 저장소와 MSA와 상호작용할 것이다.

여기서 쓰인 흥미로운 부가 사항은 다음과 같은 응용프로그램에서 Actor를 사용할 때, CompletionStage를 반환하는 것보다 부가적인 가치를 추가하는 것이다. 만약 당신의 로직이 독립적이고 매우 간단한 요청/응답 스타일이라면 Actor가 필요 없을 것이다. Actor는 Actor을 통해서 어떤 형태의 상태를 유지하고 누군가로부터 다양한 요청을 허락해야할 때 빛이 납니다. Actor의 다른 주요한 특징은 미래를 핸들링하지 않고 **Cluster Sharding** 또는 **location-transparent** 클러스터로 쉽게 확장됩니다. 

그러나 이 튜토리얼은 akka HTTP 안에서 Actor backend와 어떻게 상호작용하는지에 대해 집중합니다.

**UserRegistry** 샘플코드는 매우 간단합니다. **users**을 **Set**에 저장합니다. 메시지를 받으면 action이 정의된 케이스들과 매치합니다.

```kotlin
package io.github.dgahn.user

import akka.actor.typed.ActorRef
import akka.actor.typed.Behavior
import akka.actor.typed.javadsl.AbstractBehavior
import akka.actor.typed.javadsl.ActorContext
import akka.actor.typed.javadsl.Behaviors
import akka.actor.typed.javadsl.Receive
import com.fasterxml.jackson.annotation.JsonCreator
import com.fasterxml.jackson.annotation.JsonProperty
import java.util.*


class UserRegistry private constructor(context: ActorContext<Command?>?) :
    AbstractBehavior<UserRegistry.Command?>(context) {
    // actor protocol
    interface Command

    class GetUsers(val replyTo: ActorRef<Users?>?) :
        Command {

    }

    class CreateUser(val user: User?, val replyTo: ActorRef<ActionPerformed?>?) :
        Command {

    }

    class GetUserResponse(val maybeUser: Optional<User?>?)

    class GetUser(val name: String?, val replyTo: ActorRef<GetUserResponse?>?) :
        Command {

    }

    class DeleteUser(val name: String?, val replyTo: ActorRef<ActionPerformed?>?) :
        Command {

    }

    class ActionPerformed(val description: String?) : Command

    class User @JsonCreator constructor(
        @param:JsonProperty("name") val name: String?,
        @param:JsonProperty("age") val age: Int,
        @param:JsonProperty("countryOfRecidence") val countryOfResidence: String?
    )

    class Users(val users: MutableList<User?>?)

    private val users: MutableList<User?>? = ArrayList()
    override fun createReceive(): Receive<Command?>? {
        return newReceiveBuilder()
            .onMessage(
                GetUsers::class.java
            ) { command: GetUsers? -> onGetUsers(command) }
            .onMessage(
                CreateUser::class.java
            ) { command: CreateUser? -> onCreateUser(command) }
            .onMessage(GetUser::class.java) { command: GetUser? -> onGetUser(command) }
            .onMessage(
                DeleteUser::class.java
            ) { command: DeleteUser? -> onDeleteUser(command) }
            .build()
    }

    private fun onGetUsers(command: GetUsers?): Behavior<Command?>? {
        command!!.replyTo!!.tell(
            Users(
                Collections.unmodifiableList(
                    ArrayList(users)
                )
            )
        )
        return this
    }

    private fun onCreateUser(command: CreateUser?): Behavior<Command?>? {
        users!!.add(command!!.user)
        command.replyTo!!.tell(
            ActionPerformed(
                String.format(
                    "User %s created.",
                    command.user!!.name
                )
            )
        )
        return this
    }

    private fun onGetUser(command: GetUser?): Behavior<Command?>? {
        val maybeUser = users!!.stream()
            .filter { user: User? -> user!!.name == command!!.name }
            .findFirst()
        command!!.replyTo!!.tell(GetUserResponse(maybeUser))
        return this
    }

    private fun onDeleteUser(command: DeleteUser?): Behavior<Command?>? {
        users!!.removeIf { user: User? -> user!!.name == command!!.name }
        command!!.replyTo!!.tell(
            ActionPerformed(
                String.format(
                    "User %s deleted.",
                    command.name
                )
            )
        )
        return this
    }

    companion object {
        fun create(): Behavior<Command?>? {
            return Behaviors.setup { context: ActorContext<Command?>? ->
                UserRegistry(
                    context
                )
            }
        }
    }
}
```