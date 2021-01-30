# 5.Kotest

## 5.1 테스트 스타일

### 5.1.1 FunSpec

test() 함수에 테스트에 대한 설명과 테스트 코드를 넣을 수 있다.

```kotlin
package me.dgahn.firstweek.step4

import io.kotest.core.spec.style.FunSpec
import io.kotest.matchers.should
import io.kotest.matchers.shouldBe
import io.kotest.matchers.string.startWith

class MyFunSpecTests: FunSpec({
    test("length는 글자의 길이를 리턴해야한다.") {
        "hello".length shouldBe 5
    }
    context("컨택스트 추가 가능") {
        test("startsWith는 prefix를 확인할 수 있다.") {
            "world" should  startWith("wor")
        }
    }
})
```

### 5.1.2 StringSpec

String을 통해서 테스트를 할 수 있다.

```kotlin
package me.dgahn.firstweek.step4

import io.kotest.core.spec.style.StringSpec
import io.kotest.matchers.should
import io.kotest.matchers.shouldBe
import io.kotest.matchers.string.startWith

class MyStringTests: StringSpec({
    "length는 글자의 길이를 리턴해야한다." {
        "hello".length shouldBe 5
    }
    "startsWith는 prefix를 확인할 수 있다." {
        "world" should  startWith("wor")
    }
})
```

### 5.1.3 ShouldSpec

```kotlin
package me.dgahn.firstweek.step4

import io.kotest.core.spec.style.ShouldSpec
import io.kotest.matchers.shouldBe
import io.kotest.matchers.string.startWith

class MyShouldSpecTests : ShouldSpec({
    should("length는 글자의 길이를 리턴해야한다.") {
        "hello".length shouldBe 5
    }
    should("startsWith는 prefix를 확인할 수 있다.") {
        "world" should startWith("wor")
    }
})
```

### 5.1.4 DescribeSpec

```kotlin
package me.dgahn.firstweek.step4

import io.kotest.core.spec.style.DescribeSpec

class MyDescribeSpecTests: DescribeSpec({
    describe("score") {
        it("start as zero") {
            // test here
        }
        describe("with a strike") {
            it("adds ten") {
                // test here
            }
            it("carries strike to the next frame") {
                // test here
            }
        }

        describe("for the opposite team") {
            it("Should negate one score") {
                // test here
            }
        }
    }
})
```

### 5.1.5 DescribeSpec

```kotlin
package me.dgahn.firstweek.step4

import io.kotest.core.spec.style.BehaviorSpec

class MyBehaviorSpec: BehaviorSpec({
    given("빗자루") {
        `when`("않으면") {
            then("하늘을 날 수 있다.") {
                // test code
            }
        }
        `when`("버리면") {
            then("다시 돌아온다.") {
                // test code
            }
        }
        and("마녀") {
            `when`("마녀가 앉으면") {
                and("마녀가 웃으면") {
                    then("마녀는 날 것이다.") {
                        // test code
                    }
                }
            }
        }
    }

    xgiven("this is disabled") {
        When("disabled by inheritance from the parent") {
            then("disabled by inheritance from its grandparent") {
                // disabled test
            }
        }
    }
    given("this is active") {
        When("this is active too") {
            xthen("this is disabled") {
                // disabled test
            }
        }
    }
})
```

## 5.2 테스트 설정

### 5.2.1 테스트 실행 설정

테스트를 무시할 수 있다.

```kotlin
"should do something".config(enabled = false) {
  ...
}
```

실행시킬 OS를 설정할 수 있다.

```kotlin
"should do something".config(enabled = IS_OS_LINUX) {
  ...
}
```

응용하면 다음과 같다.

```kotlin
val disableDangerOnWindows: EnabledIf = { !it.name.startsWith("danger") || IS_OS_LINUX }

"danger will robinson".config(enabledIf = disableDangerOnWindows) {
  // test here
}

"very safe will".config(enabledIf = disableDangerOnWindows) {
 // test here
}
```

### 5.2.2 특정 테스트만 집중하기

특정 테스트만 실행하도록 설정할 수 있다.

```kotlin
class FocusExample : StringSpec({
    "test 1" {
     // this will be skipped
    }

    "f:test 2" {
     // this will be executed
    }

    "test 3" {
     // this will be skipped
    }
})
```

### 5.2.3 특정 테스트를 무시하기

"!"를 추가하면 테스트가 무시된다.

```kotlin
class BangExample : StringSpec({

  "!test 1" {
    // this will be ignored
  }

  "test 2" {
    // this will run
  }

  "test 3" {
    // this will run too
  }
})
```

### 5.2.4 X-Methods

xMethod를 통해서 테스트를 무시할 수 있다.

```kotlin
class XMethodsExample : DescribeSpec({

  xdescribe("this block and it's children are now disabled") {
    it("will not run") {
      // disabled test
    }
  }

})
```

### 5.2.5 @Ignored

@Ignored를 통해서 무시할 수 있다.

```kotlin
@Ignored
class IgnoredSpec : FunSpec() {
  init {
    error("boom") // spec will not be created so this error will not happen
  }
}
```

### 5.2.6 @EnabledIf

@EnabledIf 를 통해서 특정 조건에서만 실행시킬 수 있다.

```kotlin
class LinuxOnlyCondition : EnabledCondition() {
   override fun enabled(specKlass: KClass<out Spec>): Boolean =
      if (specKlass.simpleName?.contains("Linux") == true) IS_OS_LINUX else true
}
```

```kotlin
@EnabledIf(LinuxOnlyCondition::class)
class MyLinuxTest1 : FunSpec() {
  ..
}

@EnabledIf(LinuxOnlyCondition::class)
class MyLinuxTest2 : DescribeSpec() {
  ..
}
```

### 5.2.7 Gradle에 필터 조건을 추가하기

패키지를 통해서 필터링 할 수 있다.

```kotlin
tasks.test {
    filter {
        //include all tests from package
        includeTestsMatching("com.sksamuel.somepackage.*")
    }
}
```

## 5.3 예외 테스트

```kotlin
val exception = shouldThrow<IllegalAccessException> {
   // code in here that you expect to throw an IllegalAccessException
}
exception.message should startWith("Something went wrong")
```

## 5.4 Listener

### 5.4.1 Listener 종류
테스트 전후로 무엇인가 하고 싶을 때 사용한다. 라이프 사이클이라고 부르고 다음과 같은 리스너들이 있다.

- beforeContainer : 테스트 컨테이너가 시작되기 전 (TestType.Container)
- afterContainer : 테스트 컨테이너가 시작된 후 (TestType.Container)
- beforeEach : 테스트 케이스가 실행되기 전 (TestType.Test)
- afterEach : 테스트 케이스가 실행되고 난 후 (TestType.Test)
- beforeAny: 테스트 타입이 실행되기 전 (TestType)
- afterAny: 테스트 타입이 실행된 후 (TestType)
- beforeTest : beforeAny와 같음
- afterTest : afterAny와 같음
- beforeSpec : 개별 Spec이 인스턴스화 할 때 호출됨. beforeTest 전에 호출됨
- afterSpec: 모든 TestCase가 완료된 이후에 호출됨. 개별 Spec 사용할 때 씀
- prepareSpec: 클래스 파일 하나당 실행 준비할 때 호출됨. 기능적으로 beforeSpec과 동일함.
- finalizeSpec: 클래스 파일 하나당 실행 완료되면 호출됨. 기능적으로 afterSpec과 동일함.
- afterInvocation: 테스트 후에 반복실행하는 것이 있을 때 사용. afterTest와 동일한 기능을 가짐.
- beforeProject: 모든 테스트 전에
- afterProject: 모든 테스트 후에

### 5.4.2 DSL Method

```kotlin 
class TestSpec : WordSpec({
   beforeTest {
     println("Starting a test $it")
   }
   afterTest { (test, result) ->
     println("Finished spec with result $result")
   }
   "this test" should {
      "be alive" {
        println("Johnny5 is alive!")
      }
   }
})
```

### 5.4.3 DSL methods with functions
```kotlin
val startTest: BeforeTest = {
   println("Starting a test $it")
}

class TestSpec : WordSpec({

   // used once
   beforeTest(startTest)

   "this test" should {
      "be alive" {
         println("Johnny5 is alive!")
      }
   }
})

class OtherSpec : WordSpec({

   // used twice
   beforeTest(startTest)

   "this test" should {
      "fail" {
         fail("boom")
      }
   }
})
```

### 5.4.4 Overriding callback functions in a Spec

```kotlin
class TestSpec : WordSpec() {
    override fun beforeTest(testCase: TestCase) {
        println("Starting a test $testCase")
    }

    init {
        "this test" should {
            "be alive" {
                println("Johnny5 is alive!")
            }
        }
    }
}
```

### 5.4.5 Standalone Listener instances

원하는 곳에만 등록하기

```kotlin
class MyTestListener : TestListener {
   override suspend fun beforeSpec(spec:Spec) {
      // power up kafka
   }
   override suspend fun afterSpec(spec: Spec) {
      // shutdown kafka
   }
}


class TestSpec : WordSpec({
    listener(MyTestListener())
    // tests here
})
```

모두 등록하기

```kotlin
@AutoScan
object MyProjectListener : ProjectListener {
  override suspend fun beforeProject() {
    println("Project starting")
  }
  override suspend fun afterProject() {
    println("Project complete")
  }
}
```

### 5.4.6 System Out Listener

표준 출력이 나오는 것에 대해서 에러를 발생하게 해준다.

```kotlin
class MyTestSpec : DescribeSpec({

    listener(NoSystemOutListener)

    describe("All these tests should not write to standard out") {
        it("silence in the court") {
          println("boom") // failure
        }
    }
})
```

테스트에 쓰인 시간을 출력하기 위한 예제다.

```kotlin
object TimerListener : TestListener {

  var started = 0L

  override fun beforeTest(testCase: TestCase): Unit {
    started = System.currentTimeMillis()
  }

  override fun afterTest(testCase: TestCase, result: TestResult): Unit {
    println("Duration of ${testCase.description} = " + (System.currentTimeMillis() - started))
  }
}

class MyTestClass : FunSpec({
  listeners(TimerListener)
  // tests here
})

// or

object MyConfig : AbstractProjectConfig() {
    override fun listeners(): List<Listener> = listOf(TimerListener)
}
```

## 5.5 Ordering

### 5.5.1 Spec Ordering

AbstractProjectConfig을 통해서 설정할 수 있다.

```kotlin
class MyConfig: AbstractProjectConfig() {
    override val specExecutionOrder = ...
}
```

- Undefined: 기본 값으로 런타임에서 발견된 순서로대로 진행
- Lexicographic: 사전 순으로
- Random: 랜덤 순으로
- Annotated: @Order로 생성된 순으로

```kotlin
@Order(1)
class FooTest : FunSpec() { }

@Order(0)
class BarTest: FunSpec() {}

@Order(1)
class FarTest : FunSpec() { }

class BooTest : FunSpec() {}
```

### 5.5.2 Test Ordering

- Sequential :순서대로
    ```kotlin
    class SequentialSpec : StringSpec() {

        override fun testCaseOrder(): TestCaseOrder? = TestCaseOrder.Sequential

        init {
        "foo" {
            // I run first as I'm defined first
        }

        "bar" {
            // I run second as I'm defined second
        }
        }
    }
    ```
- Random: 랜덤으로
    ```kotlin
    class RandomSpec : StringSpec() {

    override fun testCaseOrder(): TestCaseOrder? = TestCaseOrder.Random
        init {
            "foo" {
                // This test may run first or second
            }

            "bar" {
                // This test may run first or second
            }
        }
    }   
    ```
- Lexicographic: 사전 순으로
    ```kotlin
    class LexicographicSpec : StringSpec() {

        override fun testCaseOrder(): TestCaseOrder? = TestCaseOrder.Lexicographic

        init {
            "foo" {
                // I run second as bar < foo
            }

            "bar" {
                // I run first as bar < foo
            }
        }
    }
    ```