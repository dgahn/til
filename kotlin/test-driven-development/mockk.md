# Mockk

## 테스트 더블 

- 스턴트맨이란 단어는 콩글리쉬다. 실제 영어는 스턴트 더블

- 스턴트 더블이란 용어로부터 가져온 것으로

- 오리지널 객체를 사용해서 테스트를 진행하기가 어려울 경우 이를 대신해서 테스트를 진행할 수 있도록 만들어주는 객체

## Mock을 써야하는 경우

- 환경 구축을 위한 작업시간이 많이 걸리는 경우

- 특정 모듈의 작업이 진행되지 않은 경우

- 타 부서와의 협의나 정책이 필요한 경우

## 테스트 더블의 종류

- Dummy Object

- Test stub

- Fake Object

- Test Spy

- Mock

## Dummy Object

단순히 인스턴스화가 가능한 객체를 의미한다.

```kotlin
import io.kotest.core.spec.style.FunSpec

class AccountServiceKtTest: FunSpec({

    test("아이디와 비밀번호를 입력하면 로그인이 된다.") {
        val id = "test"
        val password = "1234"
        val repo = AccountRepository()
        val service = AccountService(repo)
        service.signIn(id, password)
        service.signIn(id, password) shouldBe true
    }

})
```

```kotlin
class AccountService(private val repo: AccountRepository) {
    fun signIn(id: String, password: String): Boolean {
        return repo.findById(id) == password
    }
}
```

```kotlin
class AccountRepository {

    fun findById(id: String): String {
        TODO("아직 구현되지 않음")
    }

}
```

리턴 값이 필요하지 않는 경우 유용할 수 있다.


## Test Stub 

인스턴스화된 객체가 특정 상태나 모습를 대표한다.

```kotlin
class AccountRepository {

    fun findById(id: String): String = "1234"

}
```

## Fake Object

여러 개의 인스턴스를 대표할 수 있는 경우이거나, 좀 더 복잡한 구현이 들어가 있는 객체

```kotlin
import io.kotest.core.spec.style.FunSpec

class AccountServiceKtTest: FunSpec({

    test("아이디와 비밀번호를 입력하면 로그인이 된다.") {
        val id = "test1"
        val password = "12345"
        val repo = AccountRepository()
        val service = AccountService(repo)
        service.signIn(id, password) shouldBe true
    }

})
```

좀 더 다양한 케이스에 대한 상태를 가진다.

```kotlin
class AccountRepository {

    fun findById(id: String): String = if(id == "test") "1234" else "12345"

}
```

좀 더 구체적으로 구현하면 다음과 같다.

```kotlin
class AccountRepository {

    private val map = mapOf("test" to "1234", "test1" to "12345")

    fun findById(id: String): String = map[id]!!

}
```

## Spy

객체가 한 행위를 감시하는 것을 의미한다.

```kotlin
class AccountRepository {

    private val map = mapOf("test" to "1234", "test1" to "12345")

    var count = 0

    fun findById(id: String): String {
        count ++
        return map[id]!!
    }

}
```

```kotlin
class AccountServiceKtTest: FunSpec({

    test("아이디와 비밀번호를 입력하면 로그인이 된다.") {
        val id = "test"
        val password = "1234"
        val repo = AccountRepository()
        val service = AccountService(repo)
        service.signIn(id, password) shouldBe true
        repo.count shouldBe 1
    }

})
```

## Mock

테스트 더블이라고 생각하면 된다.

https://docs.microsoft.com/ko-kr/archive/msdn-magazine/2007/september/unit-testing-exploring-the-continuum-of-test-doubles


## Mockk Example

간한 mockk 예제

```kotlin
    test("Mock으로 로그인이 가능하다.") {
        val id = "test"
        val password = "1234"
        val repo = mockk<AccountRepository>()
        every { repo.findById(id) } returns password
        val service = AccountService(repo)
        service.signIn(id, password) shouldBe true

        verify { repo.findById(id) }
        confirmVerified(repo)
    }
```

```kotlin
class AccountRepository {

    fun findById(id: String): String {
        TODO("미구현")
    }

}
```
