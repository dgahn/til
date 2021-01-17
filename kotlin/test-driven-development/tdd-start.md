# 2. TDD 시작

현재 우리가 어떤식으로 개발하고 있는지 그리고 TDD를 왜 해야하는지에 대해서 의논해보고 실제 예제를 작성하면서 TDD의 장/단점을 알아본다.

- 2.1 TDD 이전의 개발
- 2.2 TDD란?
- 2.3 TDD 예제 작성(암호 검사기)
- 2.4 TDD 흐름

<br />

## 2.1 TDD 이전의 개발

#### 한 번에 코드 구현 -> 테스트 -> 디버깅 및 코드 수정

- 어떤 클래스 또는 인터페이스가 있어야할지 설계를 한다.
- 어떤 방식으로 기능을 구현할지 고민을 하고 구현을 한다.
- 구현할 기능에 대해서 테스트를 수행 또는 테스트 코드를 작성한다.

#### 문제점
- 원인을 찾기 위해 한번에 많은 코드를 탐색해야한다.
- 디버깅을 위한 로그를 추가해야한다.
- 개발 도구가 제공하는 디버거를 사용한다.

즉, 코드를 실제 코드를 작성하는 시간보다 버그가 발생했을 때, 찾는 시간이 더 오래걸린다.

테스트를 진행하기 위해 많은 시간 노력이 투자되어야 한다.

<br />

## 2.2 TDD란?

- TDD는 기능 구현보다 어떻게 동작해야하는지에 대한 테스트부터 구현하는 것을 말한다.

- 기능 검증을 위한 테스트 코드를 먼저 구현함으로써 기능이 올바르게 동작하도록 코드 구현하는 것이다.

이해를 위해 간단한 덧셈 기능을 TDD로 구현해보자.

- build.gradle.kts 확인
    ```kotlin
    plugins {
        kotlin("jvm") version "1.4.10"
    }

    group = "me.dgahn"
    version = "0.1.0"

    repositories {
        mavenCentral()
    }

    dependencies {
        implementation(kotlin("stdlib"))
        testImplementation("io.kotest:kotest-runner-junit5:4.3.2")
        testImplementation("io.kotest:kotest-assertions-core-jvm:4.3.2")
    }

    tasks.withType<Test> {
        useJUnitPlatform()
    }
    ```

### 2.2.1 검증하는 코드 작성

- 계산기에 해당 하는 클래스를 결정

- 계산기에서 덧셈 기능을 담당할 메서드의 이름을 결정

- 파라미터로 무엇을 넣을지 결정

    > me/dgahn/firstweek/step1/CalculatorTest.kt
    ```kotlin
    package me.dgahn.firstweek.step1

    import io.kotest.core.spec.style.FunSpec
    import io.kotest.matchers.shouldBe

    class CalculatorTest : FunSpec({
        val calculator = Calculator()

        test("1 + 2는 3이다") {
            val result = calculator.plus(1, 2)
            result shouldBe 3
        }
    })
    ```


### 2.2.2 코드를 실행할 수 있는 단계까지 구현

- 기능에 대해 완벽하게 구현하는 것이 아니다.

- 일단, 테스트가 실행 가능한 상태까지 기능 구현

    > me/dgahn/firstweek/step1/Calculator.kt
    ```kotlin
    package me.dgahn.firstweek.step1

    class Calculator {

        fun plus(x: Int, y: Int): Int = 0

    }
    ```

- 테스트가 실패하는 것을 확인

- 테스트가 통과하도록 구현

    > me/dgahn/firstweek/step1/Calculator.kt
    ```kotlin
    package me.dgahn.firstweek.step1

    class Calculator {

        fun plus(x: Int, y: Int): Int = 3

    }
    ```

- 다른 테스트 케이스를 추가하여 테스트가 통과하도록 구현

    > me/dgahn/firstweek/step1/CalculatorTest.kt
    ```kotlin
    package me.dgahn.firstweek.step1

    import io.kotest.core.spec.style.FunSpec
    import io.kotest.matchers.shouldBe

    class CalculatorTest : FunSpec({
        val calculator = Calculator()

        test("1 + 2는 3이다") {
            val result = calculator.plus(1, 2)
            result shouldBe 3
        }

        test("1 + 4는 5이다") {
            val result = calculator.plus(1, 4)
            result shouldBe 5
        }
    })
    ```

    > me/dgahn/firstweek/step1/Calculator.kt
    ```kotlin
    package me.dgahn.firstweek.step1

    class Calculator {

        fun plus(x: Int, y: Int): Int = when {
            x == 1 && y == 2 -> 3
            else -> 5
        }

    }

- 일단, 흐름만 파악하자.

<br />

## 2.3 TDD 예제 작성(암호 검사기)

- 암호 검사기를 만들어보자.

- 검사할 규칙은 다음과 같이 3가지다.
  - 길이가 8글자 이상
  - 0부터 9 사이의 숫자를 포함
  - 대문자 포함
- 3개 만족 -> 강함, 2개 만족 -> 보통, 1개 만족 -> 약함



### 2.3.1 모든 규칙을 만족하는 경우

- 테스트 케이스를 설계할 때는 가장 쉬운 것부터 설정하는 것이 편하다.

- 강함에 대한 테스트 케이스를 작성하고 아까와 같은 단계를 거친다.
  - 검증 코드 작성 -> 테스트 실행 해당 케이스에 대한 테스트가 완료되도록 수정 -> 테스트 실행  

    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    import io.kotest.core.spec.style.FunSpec
    import io.kotest.matchers.shouldBe

    class PasswordStrengthMeterTest: FunSpec({
        test("3가지 조건을 만족하면 비밀번호의 강도는 STRONG 이다.") {
            val meter = PasswordStrengthMeter()

            val result1 = meter.meter("ab12!@AB")
            result1 shouldBe PasswordStrength.STRONG
        }
    }
    ```

  - 컴파일 오류 해결 -> 테스트 실행 
    > me/dgahn/firstweek/step2/PasswordStrength.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    enum class PasswordStrength {
        STRONG
    }
    ```

    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String): PasswordStrength = PasswordStrength.STRONG

    }
    ```
  - 테스트 케이스를 추가 -> 테스트 실행
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    import io.kotest.core.spec.style.FunSpec
    import io.kotest.matchers.shouldBe

    class PasswordStrengthMeterTest: FunSpec({
        test("3가지 조건을 만족하면 비밀번호의 강도는 STRONG 이다.") {
            val meter = PasswordStrengthMeter()

            val result1 = meter.meter("ab12!@AB")
            result1 shouldBe PasswordStrength.STRONG

            val result2 = meter.meter("abc12!@Add")
            result2 shouldBe PasswordStrength.STRONG
        }

    }
    ```


### 2.3.2 길이만 8글자 미만이고 나머지 조건은 충족하는 경우

- 해당 조건에 맞는 테스트 케이스를 설계하고 실행한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
        test("길이만 8글자 미만이고 나머지 조건은 충족하면 NORMAL 이다.") {
            val meter = PasswordStrengthMeter()
            val result1 = meter.meter("ab12!@A")
            result1 shouldBe PasswordStrength.NORMAL
        }
    ... 생략
    ```

- 해당 테스트가 실행 가능하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrength.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    enum class PasswordStrength {
        STRONG,
        NORMAL
    }
    ```

- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String): PasswordStrength = PasswordStrength.NORMAL 

    }
    ```

- 다른 테스트까지 모두 실행 가능하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String): PasswordStrength 
            = if(s.length < 8) PasswordStrength.NORMAL else PasswordStrength.STRONG

    }
    ```

### 2.3.3 숫자를 포함하지 않고 나머지 조건을 충족하는 경우

- 해당 조건에 맞는 테스트 케이스를 설계한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
        test("숫자를 포함하지 않고 나머지 조건을 충족하면 NORMAL 이다.") {
            val meter = PasswordStrengthMeter()
            val result1 = meter.meter("abc!@AAqwe")
            result1 shouldBe PasswordStrength.NORMAL
        }
    ... 생략
    ```
- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String): PasswordStrength {
            if (s.length < 8) return PasswordStrength.NORMAL
            var containsNum = false
            for (ch in s) {
                if (ch in '0'..'9') {
                    containsNum = true
                    break
                }
            }

            if (!containsNum) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }
        
    }
    ```

- 구현 코드 / 테스트 코드를 리펙토링 한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String): PasswordStrength {
            if (s.length < 8) return PasswordStrength.NORMAL
            val containsNum = meetsContainingNumberCriteria(s)
            if (!containsNum) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }
        
    }
    ```

    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    import io.kotest.core.spec.style.FunSpec
    import io.kotest.matchers.shouldBe
    
    class PasswordStrengthMeterTest: FunSpec({

        val meter = PasswordStrengthMeter()
    
        test("3가지 조건을 만족하면 비밀번호의 강도는 STRONG 이다.") {
        
            val result1 = meter.meter("ab12!@AB")
            result1 shouldBe PasswordStrength.STRONG
    
            val result2 = meter.meter("abc12!@Add")
            result2 shouldBe PasswordStrength.STRONG
        }
    
        test("길이만 8글자 미만이고 나머지 조건은 충족하면 NORMAL 이다.") {
            val result1 = meter.meter("ab12!@A")
            result1 shouldBe PasswordStrength.NORMAL
        }
    
        test("숫자를 포함하지 않고 나머지 조건은 충족하면 NORMAL 이다.") {
            val result1 = meter.meter("abc!@AAqwe")
            result1 shouldBe PasswordStrength.NORMAL
        }
    }
    ```

- 코드 수정으로 인한 테스트 코드의 실패가 없는지 다시 테스트를 실행한다.


### 2.3.4 값이 없는 경우

- 값이 없는 경우에 대한 로직 처리는 어떻게 할지 고민해보자
  - 예외을 발생시킨다.
  - PasswordStrength.INVALID 리턴

- 해당 조건에 맞는 테스트 케이스를 설계한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
    test("값이 Null 이면 INVALID 다.") {
        val result = meter.meter(null)
        result shouldBe PasswordStrength.INVALID
    }
    ... 생략
    ```

- 해당 테스트가 실행 가능하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrength.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    enum class PasswordStrength {
        STRONG,
        NORMAL,
        INVALID
    }
    ```

    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String?): PasswordStrength {
            if (s.length < 8) return PasswordStrength.NORMAL
            var containsNum = false
            for (ch in s) {
                if (ch in '0'..'9') {
                    containsNum = true
                    break
                }
            }

            if (!containsNum) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }
        
    }
    ```

- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {
        fun meter(s: String): PasswordStrength {
            if (s.isNullOrBlank()) return PasswordStrength.INVALID
            if (s.length < 8) return PasswordStrength.NORMAL
            val containsNum = meetsContainingNumberCriteria(s)
            if (!containsNum) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }
        
    }
    ```

- 다른 테스트까지 모두 실행 가능하도록 코드를 수정한다.



### 2.3.5 길이가 8글자 이상인 조건만 충족하는 경우

- 해당 조건에 맞는 테스트 케이스를 설계한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
    test("대문자를 포함하지 않고 나머지 조건을 충족하면 NORMAL 이다.") {
        val result = meter.meter("ab12!@df")
        result shouldBe PasswordStrength.NORMAL
    }
    ... 생략
    ```

- 해당 테스트가 실행 가능하도록 코드를 수정한다.

- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String?): PasswordStrength {
            if (s.isNullOrBlank()) return PasswordStrength.INVALID
            if (s.length < 8) return PasswordStrength.NORMAL
            val containsNum = meetsContainingNumberCriteria(s)
            if (!containsNum) return PasswordStrength.NORMAL
            val containsUpp = meetsContainingUppercaseCriteria(s)
            if (!containsUpp) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }
        
        private fun meetsContainingUppercaseCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch.isUpperCase()) {
                    return true
                }
            }
            return false
        }
    }
    ```

- 다른 테스트까지 모두 실행 가능하도록 코드를 수정한다.



### 2.3.6 숫자 포함 조건만 충족하는 경우

- 해당 조건에 맞는 테스트 케이스를 설계한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
    test("길이가 8글자 이상인 조건만 충족하면 WEAK 다.") {
        val result = meter.meter("abdefghi")
        result shouldBe PasswordStrength.WEAK
    }
    ... 생략
    ```

- 해당 테스트가 실행 가능하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrength.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    enum class PasswordStrength {
        STRONG,
        NORMAL,
        WEAK,
        INVALID
    }
    ```

- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String?): PasswordStrength {
            if (s.isNullOrBlank()) return PasswordStrength.INVALID

            val lengthEnough = s.length >= 8
            val containsNum = meetsContainingNumberCriteria(s)
            val containsUpp = meetsContainingUppercaseCriteria(s)

            if(lengthEnough && !containsNum && !containsUpp) return PasswordStrength.WEAK

            if (!lengthEnough) return PasswordStrength.NORMAL
            if (!containsNum) return PasswordStrength.NORMAL
            if (!containsUpp) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }

        private fun meetsContainingUppercaseCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch.isUpperCase()) {
                    return true
                }
            }
            return false
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }

    }
    ```

- 다른 테스트까지 모두 실행 가능하도록 코드를 수정한다.

### 2.3.7 숫자 포함 조건만 충족하는 경우

- 해당 조건에 맞는 테스트 케이스를 설계한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
    test("숫자 포함 조건만 충족하면 WEAK 다.") {
        val result = meter.meter("12345")
        result shouldBe PasswordStrength.WEAK
    }
    ... 생략
    ```

- 해당 테스트가 실행 가능하도록 코드를 수정한다.

- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String?): PasswordStrength {
            if (s.isNullOrBlank()) return PasswordStrength.INVALID

            val lengthEnough = s.length >= 8
            val containsNum = meetsContainingNumberCriteria(s)
            val containsUpp = meetsContainingUppercaseCriteria(s)

            if(lengthEnough && !containsNum && !containsUpp) return PasswordStrength.WEAK
            if(!lengthEnough && containsNum && !containsUpp) return PasswordStrength.WEAK

            if (!lengthEnough) return PasswordStrength.NORMAL
            if (!containsNum) return PasswordStrength.NORMAL
            if (!containsUpp) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }

        private fun meetsContainingUppercaseCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch.isUpperCase()) {
                    return true
                }
            }
            return false
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }

    }
    ```

- 다른 테스트까지 모두 실행 가능하도록 코드를 수정한다.

### 2.3.8 숫자 포함 조건만 충족하는 경우

- 해당 조건에 맞는 테스트 케이스를 설계한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
    test("대문자 포함 조건만 충족하면 WEAK 다.") {
        val result = meter.meter("ABZEF")
        result shouldBe PasswordStrength.WEAK
    }
    ... 생략
    ```

- 해당 테스트가 실행 가능하도록 코드를 수정한다.

- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String?): PasswordStrength {
            if (s.isNullOrBlank()) return PasswordStrength.INVALID

            val lengthEnough = s.length >= 8
            val containsNum = meetsContainingNumberCriteria(s)
            val containsUpp = meetsContainingUppercaseCriteria(s)

            if(lengthEnough && !containsNum && !containsUpp) return PasswordStrength.WEAK
            if(!lengthEnough && containsNum && !containsUpp) return PasswordStrength.WEAK
            if(!lengthEnough && !containsNum && containsUpp) return PasswordStrength.WEAK

            if (!lengthEnough) return PasswordStrength.NORMAL
            if (!containsNum) return PasswordStrength.NORMAL
            if (!containsUpp) return PasswordStrength.NORMAL
            return PasswordStrength.STRONG
        }

        private fun meetsContainingUppercaseCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch.isUpperCase()) {
                    return true
                }
            }
            return false
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }

    }
    ```

- 다른 테스트까지 모두 실행 가능하도록 코드를 수정한다.

### 2.3.9 meter() 메서드 리펙토링

- 첫 번째 리펙토링
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    package me.dgahn.firstweek.step2

    class PasswordStrengthMeter {

        fun meter(s: String?): PasswordStrength {
            if (s.isNullOrBlank()) return PasswordStrength.INVALID

            var metCounts = 0
            val lengthEnough = s.length >= 8
            if (lengthEnough) metCounts++
            val containsNum = meetsContainingNumberCriteria(s)
            if (containsNum) metCounts++
            val containsUpp = meetsContainingUppercaseCriteria(s)
            if (containsUpp) metCounts++

            if(metCounts == 1) return PasswordStrength.WEAK
            if(metCounts == 2) return PasswordStrength.NORMAL

            return PasswordStrength.STRONG
        }

        private fun meetsContainingUppercaseCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch.isUpperCase()) {
                    return true
                }
            }
            return false
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }

    }
    ```
- 두 번째 리펙토링
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    class PasswordStrengthMeter {

        fun meter(s: String?): PasswordStrength {
            if (s.isNullOrBlank()) return PasswordStrength.INVALID

            var metCounts = 0
            if (s.length >= 8) metCounts++
            if (meetsContainingNumberCriteria(s)) metCounts++
            if (meetsContainingUppercaseCriteria(s)) metCounts++

            if(metCounts == 1) return PasswordStrength.WEAK
            if(metCounts == 2) return PasswordStrength.NORMAL

            return PasswordStrength.STRONG
        }

        private fun meetsContainingUppercaseCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch.isUpperCase()) {
                    return true
                }
            }
            return false
        }

        private fun meetsContainingNumberCriteria(s: String): Boolean {
            for (ch in s) {
                if (ch in '0'..'9') {
                    return true
                }
            }
            return false
        }

    }
    ```

### 2.3.10 아무 조건도 충족하지 않는 경우

- 해당 조건에 맞는 테스트 케이스를 설계한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeterTest.kt
    ```kotlin
    ... 생략
    test("아무 조건도 충족하지 않으면 WEAK 다") {
        val result = meter.meter("abc")
        result shouldBe PasswordStrength.WEAK
    }
    ... 생략
    ```

- 해당 테스트가 실행 가능하도록 코드를 수정한다.

- 해당 테스트가 통과하도록 코드를 수정한다.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    ... 생략
        fun meter(s: String?): PasswordStrength {
        if (s.isNullOrBlank()) return PasswordStrength.INVALID

        var metCounts = 0
        if (s.length >= 8) metCounts++
        if (meetsContainingNumberCriteria(s)) metCounts++
        if (meetsContainingUppercaseCriteria(s)) metCounts++

        if(metCounts <= 1) return PasswordStrength.WEAK
        if(metCounts == 2) return PasswordStrength.NORMAL

        return PasswordStrength.STRONG
    }
    ... 생략
    ```

- 다른 테스트까지 모두 실행 가능하도록 코드를 수정한다.

### 2.3.11 코드 가독성 개선

- metCounts 변수를 수정하는 부분을 개선해보자.
    > me/dgahn/firstweek/step2/PasswordStrengthMeter.kt
    ```kotlin
    ... 생략
    fun meter(s: String?): PasswordStrength {
        if (s.isNullOrBlank()) return PasswordStrength.INVALID

        val metCounts = getMetCriteriaCounts(s)

        if(metCounts <= 1) return PasswordStrength.WEAK
        if(metCounts == 2) return PasswordStrength.NORMAL

        return PasswordStrength.STRONG
    }

    private fun getMetCriteriaCounts(s: String): Int {
        var metCounts = 0
        if (s.length >= 8) metCounts++
        if (meetsContainingNumberCriteria(s)) metCounts++
        if (meetsContainingUppercaseCriteria(s)) metCounts++
        return metCounts
    }
    ... 생략
    ```

- 코드의 가독성이 좋다는 것은 세부 로직을 어떻게 구현 했는지는 몰라도 비즈니스 로직이 어떤지는 한눈에 볼 수 있는 것을 의미한다.

<br />

## 2.4 TDD 흐름

지금까지 코드를 작성한 흐름은 다음과 같다
> 테스트할 기능에 대한 설계 -> 테스트 -> 실행 가능하도록 코드 수정 -> 테스트를 통과하도록 코드 수정 -> 테스트 -> 전체 테스트 -> 리펙토링 -> 테스트

테스트를 통해 코드를 추가하면서 기존 테스트를 깨지지 않도록 하는 것이 중요하다.

- 테스트가 개발을 주도
    - 테스트가 추가될 때마다 기능이 확장되어 기능의 완성도가 높아진다. 즉, 테스트가 기능 개발을 이끌어 간다.
    - 중간 중간 코드에 대해서 리펙토링을 진행했다. 리펙토링을 해야할 대상은 실제 구현 코드 뿐만 아니라 테스트 코드도 포함이 된다.
    - 잘 동작하는 코드를 리펙토링을 하는 것은 프로그램이 정상적으로 동작하지 않을 것에 대한 불안감을 주지만 테스트 코드가 존재하면 그 불안감을 어느 정도 줄여준다.

- 빠른 피드백
    - 코드 수정에 대한 피드백이 굉장히 빠르다.