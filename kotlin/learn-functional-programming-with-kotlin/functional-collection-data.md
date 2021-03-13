# 컬렉션으로 데이터 다루기

## 간단한 리스트 자료구조 만들기

- cons: construct의 줄임말로 함수형에서 리스트의 구성요소를 의미한다.

cons는 head와 tail로 구성되고 head는 cons가 가지는 값을 가지고, tail은 nil(빈 cons)이나 다른 cons를 가리킨다.

이 구조를 코드로 작성하면 다음과 같다.

```kotlin
sealed class FunList<out T> {
    object Nil : FunList<Nothing>()
    data class Cons<out T>(val head: T, val tail: FunList<T>) : FunList<T>()
}
```

sealed class로 묶었기 때문에 T에는 Nil 또는 Cons 타입만 가지게 된다.

## addHead 함수 만들기

FunList의 맨 앞에 값을 추가하는 함수를 만들어보자.

```kotlin
fun <T> FunList<T>.addHead(head: T): FunList<T> = FunList.Cons(head, this)
```

함수형 컬렉션에서 제공하는 함수들은 불변성을 지키고 부수효과를 없애기 위해서 **원본 데이터를 변경하지 않고 가공된 데이터를 매번 새로 생성하여 반환하는 특징**이 있다. 중요한 것은 생성 비용을 최소화하지 않으면 비효율적으로 연산될 수 있다. 생성 비용을 최소화하기 위해서 게으른 평가와 내부 캐싱을 사용한다.

## appendTail 함수 만들기

FunList에 끝에 값을 추가하는 함수를 만들자.

```kotlin
tailrec fun <T> FunList<T>.appendTail(value: T, acc: FunList<T> = FunList.Nil) : FunList<T> =  when (this) {
    FunList.Nil -> FunList.Cons(value, acc).reverse()
    is FunList.Cons -> tail.appendTail(value, acc)
}
```

appendTail 함수를 실행하기 위한 reverse 함수는 아래와 같다.

```kotlin
tailrec fun <T> FunList<T>.reverse(acc: FunList<T> = FunList.Nil): FunList<T> = when(this) {
    FunList.Nil -> acc
    is FunList.Cons -> tail.reverse(acc.addHead(head))
}
```

## 꼬리 재귀로 작성한 appnedTail 함수의 시간 복잡도

appendTail에서 0(1) reverse에서 o(n)를 수행해야함으로 결과적으로 o(n)이 된다.

## getTail 함수 만들기

꼬리 리스트를 얻어오기 위한 getTail 함수

```kotlin
fun <T> FunList<T>.getTail(): FunList<T> = when(this) {
    FunList.Nil -> throw NoSuchElementException()
    is FunList.Cons -> tail
}
```

## getHead 함수 만들기

