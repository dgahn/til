# 함수형 프로그래밍이란?

함수형 프로그래밍의 특징은 다음과 같다.

- 불변성

- 참조 투명성

- 일급 함수

- 게으른 실행

1장에서는 함수형 프로그램의 특징을 명령형 프로그래밍과 비교하여 설명한다.

## 1.1 함수형 프로그래밍의 특징

함수형 프로그래밍의 객체지향형 프로그래밍과 반대되는 개념이 아니라, 오히려 명령형 프로그래밍과 비교되는 개념이다.

함수형 프로그래밍으로 개발하는데에 대한 이점은 다음과 같다.

- **부수효과가 없는 프로그램**을 만들 수 있어 동시성 프로그래밍에 적합하다.

- 코드의 복잡도가 낮아 **간결한 코드**를 만들 수 있고, 모듈성이 높아져 유지보수하기 쉽다.

- 프로그램의 예측성을 높여 컴파일러가 **효율적으로 실행되는 코드**를 만들어 준다.

함수형 프로그래밍이 오래됬는데 현재 트렌드가 되고 있는 이유는 현 시대의 프로그램이 해결해야하는 문제에 적합한 솔루션이 되기 때문이다.

## 1.2 순수한 함수란 무엇인가?

수학의 함수는 어떤 입력 값에 대해서 항상 동일한 결과만 낸다. 이를 프로그램으로 모델링 한 것이 순수함수다.

순수 함수의 특징

- 동일한 입력으로 실행하면 항상 동일한 결과가 나온다.
- 부수효과가 없다.

### 동일 입력 동일 출력

순수하지 않는 함수의 예는 다음과 같다.

```kotlin
fun main() {
    println(impureFunction(1, 2)) // "13" 출력
    z = 20
    println(impureFunction(1, 2)) // "23" 출력
}

var z = 10

fun impureFunction(x: Int, y: Int): Int = x + y + z
```

**impureFunction** 함수는 위치에 따라 같은 입력에 대해서 다른 결과를 내고 있다. 이는 **impureFunction** 함수가 어떤 결과를 낼지 예측하기 어렵다는 것이다.

같은 입력에 대해서 같은 결과를 낸다는 것은 결과에 대해서 추론이 가능하다는 말이다. 그 덕분에 컴파일 시점에 코드를 최적화하거나 오류 코드를 예측하고 경고하는 등 많은 것을 할 수 있다.
또한 동시성 프로그래밍에서는 공유 자원이 변경될 걱정이 없다.

### 부수효과 없는 코드

부수효과란 함수가 실행되는 과정에서 외부의 상태를 수정하는 것을 말한다.

부수효과의 예는 다음과 같다.

```kotlin
var z = 10

fun impureFunctionWithSideEffect(x: Int, y: Int): Int {
    z = y
    return x + y
}
```

**impureFunctionWithSideEffect** 함수는 입력이 같은 경우 같은 결과를 내지만 **z**를 변경하고 있으므로 순수함수가 아니다.

### 순수한 함수의 효과와 그 외 고려사항

순수 함수로 이루어진 동시성 프로그래밍은 공유 자원이 변경될 걱정이 없다. 순수 하지 못한 작업, 파일 입출력이나 네트워크 통신 등의 작업들은 모듈화하여 분리하는 방식으로 접근한다.

## 1.3 부수효과 없는 프로그램 작성하기

부수효과는 함수의 반환 값이 아닌 외부의 상태에 영향을 미치는 것을 말한다.

### 공유 변수 수정으로 인한 부수효과

앞서 살펴봤지만 함수 내에서 전역 변수와 같은 공유 변수를 수정하면 부수효과가 발생하고, 이 변수를 참조하는 변수는 결과가 외부 요인에 의해 달라진다.

```kotlin
fun main() {
    println(impureFunction(1, 2))    // "13" 출력
    println(withSideEffect(10, 20))  // "30" 출력
    println(impureFunction(1, 2))    // "23" 출력
}

var z = 10

// 순수하지 않은 함수
fun impureFunction(x: Int, y: Int): Int = x + y + z

// 부수효과가 있는 함수
fun withSideEffect(x: Int, y: Int): Int {
    z = y
    return x + y
}
```

**withSideEffect** 함수는 함수의 결과값인 x + y와 관계없는 외부 변수 z의 값을 변경하였다. 이런 노이즈 코드는 
프로그래머가 예상할 수 없는 불확실성을 생성하게 된다.

### 객체의 상태 변경으로 인한 부수효과

객체의 상태를 변경하는 것도 부수효과를 만든다. 

```kotlin
data class MutablePerson(var name: String, var age: Int)

fun addAge(Person: MutablePerson, num: Int) {
    person.age += num
}
```

addAge 함수는 매개변수로 받은 MutablePerson 객체를 수정하고 있다. 만약 다른 함수나 모듈에서 동일한 인스턴스를 참조하면 이 부수효과의 영향을 받을 수 있다.

그래서 함수형 프로그래밍에서는 수정할 때는 부수효과를 없애기 위해서 불변객체로 생성하고 변경한 새로운 객체를 생성한다.

```kotlin
data class ImmutablePerson(val name: String, val age: Int)

fun addAge(person: ImmutablePerson, num: Int): ImmutablePerson {
    return ImmutablePerson(person.name, person.age + num)
}
```

불필요한 부수효과를 최소화하고 부수효과를 수반해야만 하는 작업은 반드시 순수한 영역과 분리해야한다.

## 1.4 참조 투명성으로 프로그램을 더 안전하게 만들기

순수한 함수는 참조 투명성을 만족시킨다. 참조 투명성이라, **프로그램의 변경 없이 어떤 표현식을 값으로 대체할 수 있다**라는 뜻이다. 

### 참조 투명하지 않는 함수

다음은 참조 투명하지 않는 함수의 예다.

```kotlin
var someName: String = "Joe"

fun hello1() {
    println("Hello $someName")
}
```

**hello1** 함수는 전역 변수를 참조하고 있기 때문에 참조 투명하지 않은 함수이다.

다음 함수를 보자.

```kotlin
fun hello2(someName: String) {
    println("Hello $someName")
}
```

전역 변수를 참조하지 않고 같은 값에 대해서 같은 결과를 낸다. 하지만 콘솔에 출력하는 작업 자체가 부수효과를 일으키기 때문에 여전히 참조에 투명하다고 보기 어렵다.

### 참조 투명한 함수

```kotlin
fun main() {
    val result = transparent("Joe")
    print(result)
}

fun transparent(name: String) {
    return "Hello $name"
}

fun print(helloStr: String) {
    println(helloStr)
}
```

hello2 함수를 transparent 함수로 분리하였다. 이제는 참조에 투명하다. 어떻게 호출해도 부수효과가 생성되지 않는다. print 함수가 콘솔 출력을 하기 때문에 여전히
부수효과를 내고 있지만 어쩔 수 없다. 이렇게 함수형 프로그래밍은 참조 투명성을 지키기 위해 분리하는 방향으로 설계가 진행되어야 한다.

## 1.5 일급 함수란?

일급 함수를 알기 위해서는 일급 객체가 무엇인지를 알아야 한다.

### 일급 객체(first-class object)

자바를 비롯한 대부분의 객체지향 언어는 일급 객체를 지원한다. 일급 객체의 3가지 조건은 다음과 같다.

- 객체를 함수의 매개변수로 넘길 수 있다.

- 객체를 함수의 반환값으로 돌려 줄 수 있다.

- 객체를 변수나 자료구조에 담을 수 있다.

일급 객체인 **Any**의 예제를 보자.

```kotlin
fun doSomthingWithAny(any: Any) {
    //do something
}

// Any를 함수의 반환값으로 돌려 줄 수 있다.
fun doSomethingWithAny(): Any {
    return Any()
}

// Any를 자료구조에 담을 수 있다.
var anyList: List<Any> = listOf(Any())
```

### 일급 함수(first-class function)

다음 조건을 만족하면 일급 함수라고 한다.

- 함수를 함수의 매개변수로 넘길 수 있다.

- 함수를 함수의 반환값으로 돌려 줄 수 있다.

- 함수를 변수나 자료구조에 담을 수 있다.

일급 함수의 조건을 만족하는 예제는 다음과 같다.

```kotlin
// 함수를 함수의 매개변수로 넘길 수 있다.
fun doSomehting(func: (Int) -> String) {
    // do something
}

// 함수를 함수의 반환값으로 돌려 줄 수 있다.
fun doSomething(): (Int) -> String {
    return { value -> value.toString() }
}

// 함수를 List 자료구조에 담을 수 있다.
var funcList: List<(Int) -> String> = listOf({ value -> value.toString() })
```

함수형 프로그래밍은 일급 함수를 통해서 더 높은 추상화가 가능하고, 코드의 재사용성을 높일 수 있다.

## 1.6 일급 함수를 이용한 추상화와 재사용성 높이기

일급 함수를 사용하면 명령형이나 객체지향 프로그래밍에서 할 수 없는 추상화가 가능하다. 간단한 계산기를 예제를 통해서 그 차이를 알아보자.

### 간단한 계산기 예제

덧셈, 뺄셈 계산기를 구현하자.

```kotlin
fun main() {
    val calculator = SimpleCalculator()
    println(calculator.calculate('+', 3, 1)) // "4" 출력
    println(calculator.calculate('-', 3, 1)) // "2" 출력
}

class SimpleCalculator {
    fun calculate(opeartor: Char, num1: Int, num2: Int): Int = when (operator) {
        '+' -> num1 + num2
        '-' -> num1 - num2
        else -> throw IllegalArgumentException()
    }
}
```

위 코드는 명령형 프로그래밍 방식을 따른 코드이다. 여기에 곱셈이나 나눗셈을 추가하는 경우 덧셈과 뺄셈에 영향을 미칠 수가 있다. 그러므로 확장성이 고려되지 않았고, 다른 프로그램에서 재사용하기도 어렵다.

### 객체지향적으로 개선한 계산기 예제

객체지향적으로 코드를 리팩터링하면 다음과 같다.

```kotlin
fun main() {
    val plusCalculator = OopCalculator(Plus())
    println(plusCalculator.calculate(3, 1)) // "4" 출력

    val minusCalculator = OopCalculator(Minus())
    println(minusCalculator.calculate(3, 1)) // "2" 출력
}

interface Calculator {
    fun calculate(num1: Int, num2: Int): Int
}

class Plus : Calculator {
    override fun calculate(num1: Int, num2: Int): Int {
        return num1 + num2
    }
}

class Minus : Calculator {
    override fun calculate(num1: Int, num2: Int): Int {
        return num1 - num2
    }
}

class OopCalculator(private val calculator: Calculator) {
    fun calculate(num1: Int, num2: Int) {
        if (num1 > num2 && 0 != num2) {
            return calculator.calculate(num1, num2)
        } else {
            throw IllegalArgumentException()
        }
    }
}
```

위의 코드는 다음과 같은 장점을 가진다.

- 기능을 업데이트 하는데 필요한 모듈만 수정하면 된다.
- 인터페이스를 통해서 쉽게 확장할 수 있다.
- 클래스나 함수들의 기능이 단 하나이기 때문에 재사용하기 용이하다.
- 의존성 주입 덕분에 테스트가 쉽다.

### 함수형 프로그래밍 방식으로 개선한 계산기 예제

함수형으로 프로그래밍 해보자.

```kotlin
fun main() {
    val fpCalculator = FpCalculator()

    println(fpCalculator.calculate({ n1, n2 -> n1 + n2 }, 3, 1)) // "4" 출력
    println(fpCalculator.calculate({ n1, n2 -> n1 - n2 }, 3, 1)) // "2" 출력
}

class FpCalculator {
    fun calculate(calculator: (Int, Int) -> Int, num1: Int, num2: Int): Int {
        if (num1 > num2 && 0 != num2) {
            return calculator(num1, num2)
        } else {
            throw IllegalArgumentException()
        }
    }
}
```

일급함수를 사용해 계산기의 가장 중요한 연산을 추상화하였다. 인터페이스와 클래스가 모두 없어져 코드가 훨씬 간결해졌다.

## 1.7 게으른 평가로 무한 자료구조 만들기

일반적으로 명령형 언어는 코드가 실행되는 즉시 값이 평가된다. 함수형 언어는 프로그래머가 필요한 시점에 평가할 수 있다.

```kotlin
val lazyValue: String by lazy {
    println("시간이 오래 걸리는 작업")
    "hello"
}

fun main() {
    println(lazyValue)
    println(lazyValue)
}
```

실행한 결과는 다음과 같다.

```kotlin
시간이 오래 걸리는 작업
hello
hello
```

**by lazy**는 코틀린에서 제공하는 문법으로 해당 인스턴스가 호출되는 시점에 람다식이 실행된다.

게으른 평가에서 중요한 사실은 다음과 같다.

- 람다식을 선언한 시점에 실행되지 않고 호출되는 시점에 실행된다.
- 실행한 이후에는 해당 결과를 저장하여 람다식을 실행하는 것이 아니라 저장해두고 불러온다.

**by lazy**는 한번만 평가되기 때문에 **var** 저장할 수 없다. 

### 무한대 값을 자료구조에 담다.

함수형 언어에서는 **게으른 평가**라는 특성을 이용해서 무한대 값을 자료구조에 저장할 수 있다.

```kotlin
val infiniteValue = generateSequence(0) { it + 5 }
infiniteValue.take(5).forEach{ print("$it") } // "0 5 10 15 20" 출력
```

코틀린에서는 게으르게 평가되는 자료구조인 **Sequence**를 제공한다. **generateSequence** 함수는 **Sequence** 자료구조를 만들고 
실제로 값이 평가되는 시점은 **take**를 호출하는 시점이기 때문에 이러한 표현이 가능하다.
