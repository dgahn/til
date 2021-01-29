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