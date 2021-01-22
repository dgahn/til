# 3. 테스트 코드 작성 순서

## 3.1 테스트 코드 작성 순서

이전에 테스트 케이스를 작성하는 순서는 다음과 같았다.

1. 모든 규칙을 충족하는 암호 강도는 '강함'
2. 길이만 8글자 미만이고 나머지 규칙은 충족하는 암호의 강도는 '보통'
3. 숫자를 포함하지 않고 나머지 규칙은 충족하는 암호의 강도는 '보통'
4. 값이 없는 암호의 강도는 '유호하지 않음'
5. 대문자를 포함하지 않고 나머지 규칙은 충족하는 경우
6. 길이가 8글자 이상인 규칙만 충족하는 경우
7. 숫자 포함 규칙만 충족하는 경우
8. 대문자 포함 규칙만 충족하는 경우
9. 아무 규칙도 충족하지 않는 경우

이 순서는 다음과 같은 규칙을 가지고 나오게 되었다.

1. 쉬운 경우에서 어려운 경우로 진행
2. 예외적인 경우에서 정상적인 경우

이 순서의 반대로 시행하면 구현 과정이 원활하게 진행되지 않기도 한다.

### 3.1.1 초반에 복잡한 테스트부터 시작하면 안되는 이유

테스트를 진행하기 위해 한번에 많은 코드를 작성해야하기 때문이다.

- 대문자 포함 규칙만 충족하는 경우

- 모든 규칙을 충족하는 경우

- 숫자를 포함하지 않고 나머지 규칙은 충족하는 경우


### 3.1.2 구현하기 쉬운 테스트부터 시작하기

수 분에서 십여 분 이내에 구현을 완료시킬 수 있을만큼 쉬운 테스트부터 시작한다. 암호 검사기에서는 다음 두가지가 제일 쉽다.

- 모든 조건을 충족하는 경우

- 모든 조건을 충족하지 않는 경우

책에서는 모든 조건을 충족하는 경우에 대해서 먼저 테스트를 진행하였다.

그 다음 선택지는?

- 모든 규칙을 충족하지 않는 경우

- 한 규칙만 충족하는 경우

- 두 규칙을 충족하는 경우

한가지 규칙만 체크해도 되는 로직을 테스트하는 것이 더 쉽다.

- 길이만 8글자 미만이고 나머지 규칙은 충족하는 암호의 강도는 '보통'

또는

- 길이가 8글자 이상이고 나머지 규칙은 충족하지 않은 암호의 강도는 '약함'

둘 다 길이가 8글자 이상인지 여부를 판단하는 로직만 구현하면 테스트를 통과시킬 수 있다.

이런 식으로 그다음으로 구현하기 쉬운 테스트를 선택하여 한 번에 구현하는 시간을 짧게 하여 디버깅도 쉽게 할 수 있도록 하는 것이 유리하다.

### 3.1.3 예외 상황을 먼저 테스트해야하는 이유

예외상황을 나중에 추가하는 경우 코드의 구조를 뒤집거나 코드 중간에 예외 상황을 처리하기 위해 조건문을 주옵개헛 추가하는 일이 벌어진다. 그러므로 먼저 작성해서 예외 처리를 진행해주는 것이 좋다.

### 3.1.4 완급 조절

테스트 코드를 통과시키기에 어려우면 다음과 같은 예를 통해서 구현하여 통과시키면 된다.

1. 정해진 값을 리턴
2. 값 비교를 이용해서 정해진 값을 리턴
3. 다양한 테스트를 추가하면서 구현을 일반화

예를 들어, 8글자 미만이지만 나머지 규칙은 충족하는 상황을 위 단계에 맞춰 구현을 해보자.

#### 3.1.4.1 정해진 값을 리턴
```kotlin
test("8글자 미만이지만 나머지 규칙은 충족하면 NORMAL") {
    val result1 = meter.meter("ab12!@A")
    resutl1 shouldBe PasswordStrength.NORMAL
}
```

```kotlin
class PasswordStrengthMeter {
    fun meter(s: String) = PasswordStrength.NORMAL
}
```

#### 3.1.4.2 값 비교를 이용해서 정해진 값을 리턴
```kotlin
class PasswordStrengthMeter {
    fun meter(s: String) = if(s == "ab12!@A") PasswordStrength.NORMAL 
    else PasswordStrength.STRONG
}
```


#### 3.1.4.3 다양한 테스트를 추가하면서 구현을 일반화
```kotlin
test("8글자 미만이지만 나머지 규칙은 충족하면 NORMAL") {
    val result1 = meter.meter("ab12!@A")
    resutl1 shouldBe PasswordStrength.NORMAL
    val result2 = meter.meter("Ab12!c")
    result2 shouldBe PasswordStrength.NORMAL
}
```

```kotlin
class PasswordStrengthMeter {
    fun meter(s: String) = if(s == "ab12!@A" || s == "Ab12!c") PasswordStrength.NORMAL 
    else PasswordStrength.STRONG
}
```

```kotlin
class PasswordStrengthMeter {
    fun meter(s: String) = if(s.length < 9) PasswordStrength.NORMAL 
    else PasswordStrength.STRONG
}
```

### 3.1.5 지속적인 리펙토링

테스트를 통과하면 무조건 리펙토링을 해야하는 것은 아니다. 

리펙토링은 리펙토링할 대상이 될 후보가 생기면 진행을 해야한다.

한번에 모든 코드를 리펙토링을 하는 것이 아니기 때문에 리펙토링하기도 쉽고 리펙토링을 통해 코드의 가독성도 높아진다.

> 리펙토링할 때 주의해야할 점
- 일단 동작하는 코드를 만드는 것이 중요하다.
- 코드를 잘 변경할 수 있는 능력이 중요하다.
- 코드를 쉽게 변경할 수 있는 구조여야 한다.
- 상수를 변수로 바꾸거나 변수 이름을 변경하는 것과 같은 작은 리펙토링은 발견하면 바로 시행한다.
- 메소드 추출이나 메서드 구조에 영향이 가는 것은 큰 틀에서 구현 흐름이 눈에 들어오기 시작한 뒤에 진행한다.
- 한번에 너무 많은 코드를 바꾸려고 하지 말고 전체적인 흐름을 이해한 뒤에 수정해야한다.

## 3.2 테스트 작선 순서 연습

매달 비용을 지불해야 사용할 수 있는 유료 서비스를 예제로 진행을 해보도록 하자.

- 서비스를 사용하려면 매달 1만 원을 선불로 납부한다. 납부일 기준으로 한 달 뒤가 서비스 만료일이 된다.
- 2개월 이상 요금을 납부할 수 있다.
- 10만 원을 납부하면 서비스를 1년 제공한다.

먼저, 클래스 이름을 지정하는데 만료일을 게산한다는 의미로 다음과 같이 클래스를 작성한다.

```kotlin
class ExpiryDateCalculatorTest {

}
```

#### 3.2.1 쉬운 것부터 테스트

테스트 케이스를 추가할 때는 다음 두 가지를 고려해야한다.

- 구현하기 쉬운 것부터 먼저 테스트
- 예외 상황을 먼저 테스트

예상할 수 있는 테스트 케이스는 다음과 같다.

- 1만원 납부시 한 달뒤가 만료일 
- 달의 마지막 날에 납부하면 다음달 마지막 날이 만료일
- 2만원 납부시 2개월 뒤가 만료일
- 3만원 납부시 3개월 뒤가 만료일
- 10만원 납부시 만료일은 1년 뒤가 만료일

가장 쉬운 첫 번째 테스트를 작성해보자.

```kotlin
class ExpiryDateCalculatorTest: FunSpec({

    test("만원 납부하면 한달 뒤가 만료일이 됨") {
        val billingDate = LocalDate.of(2019, 3, 1)
        val payAmount = 10_000
        
        val cal = ExpiryDateCalculator()
        val expiryDate = cal.calculateExpiryDate(billingDate, payAmount)
        
        expiryDate shouldBe LocalDate.of(2019, 4, 1)
    }

})
```

- 코드를 실행 가능한 수준으로 작성하고 테스트가 통과되도록 로직을 구성한다.

```kotlin
class ExpiryDateCalculator {

    fun calculateExpiryDate(billingDate: LocalDate, payAmount: Int): LocalDate = LocalDate.of(2019, 4, 1)

}
```

#### 3.2.2 예를 추가하면서 구현을 일반화

동일 조건의 예를 추가해서 구현을 일반화하자.

```kotlin
test("만원 납부하면 한달 뒤가 만료일이 됨") {
        val billingDate = LocalDate.of(2019, 3, 1)
        val payAmount = 10_000

        val cal = ExpiryDateCalculator()
        val expiryDate = cal.calculateExpiryDate(billingDate, payAmount)

        expiryDate shouldBe LocalDate.of(2019, 4, 1)

        val billingDate2 = LocalDate.of(2019, 5, 5)
        val payAmount2 = 10_000

        val cal2 = ExpiryDateCalculator()
        val expiryDate2 = cal2.calculateExpiryDate(billingDate2, payAmount2)

        expiryDate2 shouldBe LocalDate.of(2019, 6, 5)
}
```

테스트를 실행하면 실패할 것이고 구현 테스트를 수정해보자.


```kotlin
class ExpiryDateCalculator {

    fun calculateExpiryDate(billingDate: LocalDate, payAmount: Int): LocalDate = billingDate.plusMonths(1)

}
```

#### 3.2.3 코드 정리: 중복 제거

테스트 코드를 완성했으니 리펙토링을 진행보자.

```kotlin
class ExpiryDateCalculatorTest : FunSpec({
    
    test("만원 납부하면 한달 뒤가 만료일이 됨") {
        assertExpiryDate(
            billingDate = LocalDate.of(2019, 3, 1),
            payAmount = 10_000,
            expectedExpiryDate = LocalDate.of(2019, 4, 1)
        )

        assertExpiryDate(
            billingDate = LocalDate.of(2019, 5, 5),
            payAmount = 10_000,
            expectedExpiryDate = LocalDate.of(2019, 6, 5)
        )
    }
    
})

fun assertExpiryDate(billingDate: LocalDate, payAmount: Int, expectedExpiryDate: LocalDate) {
    val cal = ExpiryDateCalculator()
    val actualExpiryDate = cal.calculateExpiryDate(billingDate, payAmount)
    actualExpiryDate shouldBe expectedExpiryDate
}
```

#### 3.2.4 예외 상황 처리

단순히 한달 추가로 끝나지 않는 상황이 존재한다.

- 납부일이 2019-01-31이고 납부액이 1만 원이면 만료일은 2019-02-28
- 납부일이 2019-05-31이고 납부액이 1만 원이면 만료일은 2019-06-30
- 납부일이 2020-01-31이고 납부액이 1만 원이면 만료일은 2020-02-29

테스트 코드로 추가해보자.

```kotlin
    test("납부일과 한달 뒤 일자가 같지 않음") {
        assertExpiryDate(
            billingDate = LocalDate.of(2019, 1, 31),
            payAmount = 10_000,
            expectedExpiryDate = LocalDate.of(2019, 2, 28)
        )
    }
```

테스트를 실행하면 통과를 하는데 이는 라이브러리가 알아서 처리해주기 때문이다.

위에 언급된 테스트 케이스를 좀 더 추가해보자.


```kotlin
    test("납부일과 한달 뒤 일자가 같지 않음") {
        assertExpiryDate(
            billingDate = LocalDate.of(2019, 1, 31),
            payAmount = 10_000,
            expectedExpiryDate = LocalDate.of(2019, 2, 28)
        )
        assertExpiryDate(
            billingDate = LocalDate.of(2019, 5, 31),
            payAmount = 10_000,
            expectedExpiryDate = LocalDate.of(2019, 6, 30)
        )
        assertExpiryDate(
            billingDate = LocalDate.of(2020, 1, 31),
            payAmount = 10_000,
            expectedExpiryDate = LocalDate.of(2020, 2, 29)
        )
    }
```

#### 3.2.5 다음 테스트 선택: 다시 예외 상황

그 다음으로 쉬운 테스틑 다음과 같다.

- 2만 원을 지불하면 만료일이 두 달 뒤가 된다.
- 3만 원을 지불하면 만료일이 세 달 뒤가 된다.

예외 상황은 다음과 같다.

- 첫 납부일이 2019-01-30이고 만료되는 2019-02-28에 1만 원을 납부하면 다음 만료일은 2019-03-30
- 첫 납부일이 2019-05-31이고 만료되는 2019-06-30에 1만 원을 납부하면 다음 만료일은 2019-07-31

둘 중 선택을 해서 테스트를 진행하면 된다.

예외 상황을 먼저 테스트 해보자. 예외 상황을 테스트하려면 첫 납부일이 필요하다. 

#### 3.2.6 다음 테스트를 추가하기 전에 리팩토링

다음 작업이 필요하다.

- calculateExpiryDate 메서도의 파라미터로 첫 납부일 추가
- 첫 납부일, 납부일, 납부액을 담은 객체를 calculateExpiryDate 메서드에 전달

파라미터 개수가 적을 수록 코드의 가독성과 유지보수에 유리하다. 파라미터 개수가 3개 이상이 되면 객체로 바꿔 한 개로 줄이는 것이 낫다.

> payData 추가

```kotlin
class ExpiryDateCalculatorTest : FunSpec({

    test("만원 납부하면 한달 뒤가 만료일이 됨") {
        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2019, 3, 1),
                payAmount = 10_000,
            ),
            expectedExpiryDate = LocalDate.of(2019, 4, 1)
        )

        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2019, 5, 5),
                payAmount = 10_000,
            ),
            expectedExpiryDate = LocalDate.of(2019, 6, 5)
        )
    }

    test("납부일과 한달 뒤 일자가 같지 않음") {
        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2019, 1, 31),
                payAmount = 10_000,
            ),
            expectedExpiryDate = LocalDate.of(2019, 2, 28)
        )
        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2019, 5, 31),
                payAmount = 10_000,
            ),
            expectedExpiryDate = LocalDate.of(2019, 6, 30)
        )
        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2020, 1, 31),
                payAmount = 10_000,
            ),
            expectedExpiryDate = LocalDate.of(2020, 2, 29)
        )
    }

    test("첫 납부일 일자와 만료일 납부일 일자가 같지 않을 때 만료일 계산 테스트") {
        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2019, 2, 28),
                payAmount = 10_000
            ),
            expectedExpiryDate = LocalDate.of(2020, 2, 29)
        )
    }

})

fun assertExpiryDate(payData: PayData, expectedExpiryDate: LocalDate) {
    val cal = ExpiryDateCalculator()
    val actualExpiryDate = cal.calculateExpiryDate(payData)
    actualExpiryDate shouldBe expectedExpiryDate
}
```

```kotlin
class PayData(
    val firstBillingData: LocalDate? = null,
    val billingDate: LocalDate,
    val payAmount: Int
)
```

```kotlin
class ExpiryDateCalculator {

    fun calculateExpiryDate(payData: PayData): LocalDate = payData.billingDate.plusMonths(1)

}
```

#### 3.2.7 예외 상황 테스트 진행 계속

- 첫 납부일이 2019-01-31이고 만료되는 2019-02-28dp 1만 원을 납부하면 다음 만료일은 2019-03-31

```kotlin
test("첫 납부일 일자와 만료일 납부일 일자가 같지 않을 때 만료일 계산 테스트") {
        assertExpiryDate(
            payData = PayData(
                firstBillingData = LocalDate.of(2019, 1, 31),
                billingDate = LocalDate.of(2019, 2, 28),
                payAmount = 10_000
            ),
            expectedExpiryDate = LocalDate.of(2020, 2, 29)
        )
}
```

컴파일 에러를 없애주자.

```kotlin
class PayData(
    val firstBillingData: LocalDate? = null,
    val billingDate: LocalDate,
    val payAmount: Int
)
```

테스트를 통과시키자.

```kotlin
fun calculateExpiryDate(payData: PayData): LocalDate {
        if (payData.firstBillingData == LocalDate.of(2019, 1, 31)) return LocalDate.of(2019, 3, 31)

        return payData.billingDate.plusMonths(1)
}
```

다음 케이스를 추가하자

- 첫 납부일이 2019-01-30이고 만료되는 2019-02-28에 1만 원을 납부하면 다음 만료일은 2019-03-31

```kotlin
    test("첫 납부일 일자와 만료일 납부일 일자가 같지 않을 때 만료일 계산 테스트") {
        assertExpiryDate(
            payData = PayData(
                firstBillingDate = LocalDate.of(2019, 1, 31),
                billingDate = LocalDate.of(2019, 2, 28),
                payAmount = 10_000
            ),
            expectedExpiryDate = LocalDate.of(2019, 3, 31)
        )

        assertExpiryDate(
            payData = PayData(
                firstBillingDate = LocalDate.of(2019, 1, 30),
                billingDate = LocalDate.of(2019, 2, 28),
                payAmount = 10_000
            ),
            expectedExpiryDate = LocalDate.of(2019, 3, 30)
        )
    }
```

```kotlin
    fun calculateExpiryDate(payData: PayData): LocalDate {
        if (payData.firstBillingDate != null) {
            val candidateExp = payData.billingDate.plusMonths(1)
            if (payData.firstBillingDate.dayOfMonth != candidateExp.dayOfMonth) return candidateExp.withDayOfMonth(
                payData.firstBillingDate.dayOfMonth
            )
        }

        return payData.billingDate.plusMonths(1)
    }
```

다음 케이스를 추가하자.

- 첫 납부일이 2019-05-31이고 만료되는 2019-06-30dp 1만 원을 납부하면 다음 만료일은 2019-07-31

```kotlin
    test("첫 납부일과 만료일 일자가 다를 때 만원 납부") {
        assertExpiryDate(
            payData = PayData(
                firstBillingDate = LocalDate.of(2019, 5, 31),
                billingDate = LocalDate.of(2019, 6, 30),
                payAmount = 10_000
            ),
            expectedExpiryDate = LocalDate.of(2019, 7, 31)
        )
    }
```

#### 3.2.8 상수를 변수로

```kotlin
class ExpiryDateCalculator {

    fun calculateExpiryDate(payData: PayData): LocalDate {
        val addedMonths: Long = 1
        if (payData.firstBillingDate != null) {
            val candidateExp = payData.billingDate.plusMonths(addedMonths)
            if (payData.firstBillingDate.dayOfMonth != candidateExp.dayOfMonth) return candidateExp.withDayOfMonth(
                payData.firstBillingDate.dayOfMonth
            )
        }

        return payData.billingDate.plusMonths(addedMonths)
    }

}
```

#### 3.2.9 다음 테스트 선택: 쉬운 테스트

다음 테스트 케이스를 추가하자.

- 2만 원을 지불하면 만료일이 두 달 뒤가 된다.
- 3만 원을 지불하면 만료일이 석 달 뒤가 된다.

```kotlin
    test("이만원 이상 납부하면 비례해서 만료일 계산") {
        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2019, 3, 1),
                payAmount = 20_000
            ),
            expectedExpiryDate = LocalDate.of(2019, 5, 1)
        )
    }
```

테스트가 실패된다. 정상적인 실행을 위한 로직을 구현하면 다음과 같다.

```kotlin
    fun calculateExpiryDate(payData: PayData): LocalDate {
        val addedMonths = (payData.payAmount / 10_000).toLong()
        if (payData.firstBillingDate != null) {
            val candidateExp = payData.billingDate.plusMonths(addedMonths)
            if (payData.firstBillingDate.dayOfMonth != candidateExp.dayOfMonth) return candidateExp.withDayOfMonth(
                payData.firstBillingDate.dayOfMonth
            )
        }

        return payData.billingDate.plusMonths(addedMonths)
    }
```

#### 3.2.10 예외 상황 테스트 추가

- 첫 납부일이 2019-01-31이고 만료되는 2019-02-28에 2만원을 납부하면 다음 만료일은 2019-04-30

```kotlin
    test("예외 상황 테스트 추가") {
        assertExpiryDate(
            payData = PayData(
                firstBillingDate = LocalDate.of(2019, 1, 31),
                billingDate = LocalDate.of(2019, 2, 28),
                payAmount = 20_000
            ),
            expectedExpiryDate = LocalDate.of(2019, 4, 30)
        )
    }
```

테스트를 성공할 수 있도록 로직을 변경하자.

```kotlin
    fun calculateExpiryDate(payData: PayData): LocalDate {
        val addedMonths = (payData.payAmount / 10_000).toLong()
        if (payData.firstBillingDate != null) {
            val candidateExp = payData.billingDate.plusMonths(addedMonths)
            if (payData.firstBillingDate.dayOfMonth != candidateExp.dayOfMonth) {
                if (YearMonth.from(candidateExp).lengthOfMonth() < payData.firstBillingDate.dayOfMonth) {
                    return candidateExp.withDayOfMonth(YearMonth.from(candidateExp).lengthOfMonth())
                }
                return candidateExp.withDayOfMonth(payData.firstBillingDate.dayOfMonth)
            }
        }

        return payData.billingDate.plusMonths(addedMonths)
    }
```

테스트 케이스를 추가하여 다시 테스트 해보자.

```kotlin
        assertExpiryDate(
            payData = PayData(
                firstBillingDate = LocalDate.of(2019, 3, 31),
                billingDate = LocalDate.of(2019, 4, 30),
                payAmount = 30_000
            ),
            expectedExpiryDate = LocalDate.of(2019, 7, 31)
        )
```

#### 3.2.11 코드 정리

코드를 정리해보자.

```kotlin
class ExpiryDateCalculator {

    fun calculateExpiryDate(payData: PayData): LocalDate {
        val addedMonths = (payData.payAmount / 10_000).toLong()
        return if (payData.firstBillingDate != null) {
            val candidateExp = payData.billingDate.plusMonths(addedMonths)
            val dayOfFirstBilling = payData.firstBillingDate.dayOfMonth
            if (dayOfFirstBilling != candidateExp.dayOfMonth) {
                val dayLenOfCandiMon = YearMonth.from(candidateExp).lengthOfMonth()
                if (dayLenOfCandiMon < dayOfFirstBilling) {
                    candidateExp.withDayOfMonth(dayLenOfCandiMon)
                } else {
                    candidateExp.withDayOfMonth(dayOfFirstBilling)
                }
            } else {
                candidateExp
            }
        } else {
            payData.billingDate.plusMonths(addedMonths)
        }
    }

}
```

메소드를 추출해보자.

```kotlin
class ExpiryDateCalculator {

    fun calculateExpiryDate(payData: PayData): LocalDate {
        val addedMonths = (payData.payAmount / 10_000).toLong()
        return if (payData.firstBillingDate != null) {
            expiryDateUsingFirstBillingDate(payData, addedMonths)
        } else {
            payData.billingDate.plusMonths(addedMonths)
        }
    }

    private fun expiryDateUsingFirstBillingDate(payData: PayData, addedMonths: Long): LocalDate {
        val candidateExp = payData.billingDate.plusMonths(addedMonths)
        val dayOfFirstBilling = payData.firstBillingDate!!.dayOfMonth
        val isSameDayOfMonth = dayOfFirstBilling != candidateExp.dayOfMonth
        return if (isSameDayOfMonth) {
            val dayLenOfCandiMon = YearMonth.from(candidateExp).lengthOfMonth()
            val withDay = if (dayLenOfCandiMon < dayOfFirstBilling) dayLenOfCandiMon else dayOfFirstBilling
            candidateExp.withDayOfMonth(withDay)
        } else {
            candidateExp
        }
    }

}
```

#### 3.2.12 다음 테스트: 10개월 요금을 납부하면 1년 제공

테스트 케이스를 추가하자.

```kotlin
    test("10개월 요금을 납부하면 1년 제공") {
        assertExpiryDate(
            payData = PayData(
                billingDate = LocalDate.of(2019, 1, 28),
                payAmount = 100_000
            ),
            expectedExpiryDate = LocalDate.of(2020, 1, 28)
        )
    }
```

테스트를 통과하도록 구현

```kotlin
class ExpiryDateCalculator {

    fun calculateExpiryDate(payData: PayData): LocalDate {
        val addedMonths = if (payData.payAmount == 100_000) 12 else (payData.payAmount / 10_000).toLong()
        return if (payData.firstBillingDate != null) {
            expiryDateUsingFirstBillingDate(payData, addedMonths)
        } else {
            payData.billingDate.plusMonths(addedMonths)
        }
    }

    private fun expiryDateUsingFirstBillingDate(payData: PayData, addedMonths: Long): LocalDate {
        val candidateExp = payData.billingDate.plusMonths(addedMonths)
        val dayOfFirstBilling = payData.firstBillingDate!!.dayOfMonth
        val isSameDayOfMonth = dayOfFirstBilling != candidateExp.dayOfMonth
        return if (isSameDayOfMonth) {
            val dayLenOfCandiMon = YearMonth.from(candidateExp).lengthOfMonth()
            val withDay = if (dayLenOfCandiMon < dayOfFirstBilling) dayLenOfCandiMon else dayOfFirstBilling
            candidateExp.withDayOfMonth(withDay)
        } else {
            candidateExp
        }
    }

}
```

처음부터 모든 테스트 케이스를 생각할 수 있는 것은 아니다. 그러므로 테스트를 하다가 생각나는 부분에 대해서 테스트 케이스를 추가하고 리펙토링도 꾸준히 이루어져야 한다.