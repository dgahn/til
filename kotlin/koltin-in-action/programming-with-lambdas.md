# 람다로 프로그래밍 

## 1. 람다 식과 멤버 참조

### 1.1 람다 소개 : 코드 블록을 함수 인자로 넘기기

어떤 이벤트가 발생했을 때, 함수를 값처럼 처리하기 위한 방법 중 하나가 **람다**다. 

```kotlin
button.setOnClickListener(new OnClickListener() {
    @Override
    public void onClick(View view) {
        /* 클릭시 수행할 동작 */
    }
})
```

위의 익명 클래스를 람다를 이용하면 다음과 같이 변경할 수 있다.

```kotlin
button.setOnclickListener {
    /* 클릭시 수행할 동작 */
}
```

좀 더 간결하고 읽기 쉽게 바꿀 수 있다.

<br>

### 1.2 람다와 컬렉션

코틀린에서 컬렉션의 값 중 가장 큰 값을 찾는 코드는 다음과 같다.

```kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.maxBy{ it.age })
```

위와 같이 단지 함수나 프로퍼티를 반환하는 역할을 수행하는 람다는 멤버 참조로 변경할 수 있다.

```kotlin
people.maxBy(Person::age)
```

어떻게 코드가 간단하게 되는지 살펴보자.

<br>

### 1.3 람다 식의 문법

코틀린에서의 기본 람다식은 다음과 같다.

```kotlin
{ x:Int, y:Int -> x + y }
```

코틀린 람다식은 항상 중괄호로 둘러싸여 있다. Java와 다르게 인자 목록 주변에 괄호가 없다. 화살표가 인자 목록과 람다 본문을 구분해주기 때문이다.

<br>

람다 식을 변수에 저장하고 사용할 수 있다.

```kotlin
val sum = { x:Int, y: Int -> x + y }
pritnln(sum(1, 2))
```

<br>

**run**을 통해서 람다 본문에 있는 코드를 실행할 수 있다.

```kotlin
run { println(42) }
```

<br>

```kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.maxBy{ it.age })
```

위의 코드를 람다식으로 표현하면 아래와 같다.

```kotlin
people.maxBy({p: Person -> p.age})
```

<br>

람다식으로 표현한 코드가 좀 더 명확하게 어떤 동작을 하는지 코드로서 가독성이 높다. 이를 좀 더 줄이면 다음과 같이 작성할 수 있다.

먼저, 구분자가 너무 많아 가독성이 떨어지는데 함수를 호출할 때, 마지막 인자가 람다 식이라면 괄호 밖으로 빼낼 수 있다.

```kotlin
people.maxBy() { p:Person -> p.age }
```

만약, 유일한 인자가 람다라면 괄호를 없애도 된다.

```kotlin
people.maxBy { p:Person -> p.age }
```

<br>

joinToString을 이용한 람다 활용을 살펴보자.

```kotlin
val people = listOf(Person("이몽룡", 29), Person("성춘향", 31))
val names = people.joinToString(separtor=" ", transform = { p: Person -> p.name })
println(names)

>> 이몽룡 성춘향
```

이 함수 호출에서 함수를 괄호 밖으로 뺀 모습은 다음과 같다.

```kotlin
people.joinToString(" ") { p: Person -> p.name }
```

<br>

파라미터 타입은 컴파일러가 추론 할 수 있기 때문에 다음과 같이 코드를 줄일 수 있다.

```kotlin
people.maxBy { p: Person -> p.age }
people.maxBy { p -> p.age }
```
 
<br>

람다의 파라미터 이름의 디폴트는 it이다. 그래서 디폴트 이름을 사용하면 람다 식을 더 간단하게 변경할 수 있다.

```kotlin
people.maxBy { it.age }
```

<br>

>노트  
>it을 사용하는 괍습은 코드를 아주 간단하게 만들어준다. 하지만 이를 남용하면 안된다. 
>특히 람다 안에 람다가 중첩되는 경우 각 람다의 파라미터를 명시하는편이 낫다. 
>파라미터를 명시하지 않으면 it이 가리키는 파라미터가 어떤 람다에 속했는지 파악하기 어려울 수 있다. 
>문맥에서 람다 파라미터의 의미나 파라미터의 타입을 쉽게 알 수 없는 경우에도 파라미터를 명시적으로 선언하면 도움이 된다.

다음과 같이 파라미터의 타입을 추론할 문맥이 존재하지 않는 경우, 파라미터를 타입을 명시해야한다.

```kotlin
val getAge = { p: Person, p.age }
people.maxBy(getAge)
```

<br>

만약 람다식이 한줄이 아니라 여러줄로 이루어질 때는 본문의 마지막 줄이 결과 값이 된다.

```kotlin
val sum = { x: Int, y: Int ->
  println("Computing the sum of $x and $y...")
  x + y
}
```

<br>

### 1.4 현재 영역에 있는 변수에 접근

람다를 함수 안에서 선언하면 람다 앞에 선언한 로컬 변수를 사용할 수 있다.

```kotlin
fun printMessageWithPrefix(messages: Collection<String>, prefix: String) {
    message.foreach {
        println("$prefix $it")
    }
}

val errors = listOf("403 Forbidden", "404 Not Found")
printMessageWithPrefix(errors, "Error:")
```

<br>

코틀린에서는 람다 밖의 변수에 접근하여 해당 변수의 값을 변경할 수 있다.

```kotlin
fun printProblemCounts(response: Collection<String>) {
    var clientErrors = 0
    var serverErrors = 0
    response.forEach {
        if (it.startsWith("4")) {
            clientErrors++
        } else if(it.startsWith("5")) {
            serverErrors++
        }
    }

    println("$clientErrors client errors, $ServerErrors server errors")
}

val responses = listOf("200 OK", "418 I'm a teapot", "500 Internal Server Error")
printlnProblemCounts(responses)
```

코틀린에서는 위와 같이 람다 밖의 변수를 람다에서 사용한 경우 **람다가 포획한 변수**라고 부른다.

코틀린에서는 변수를 **val**로 선언하든 **var**로 선언하든 람다식에서 해당 변수를 사용할 수 있게 제공해준다. 

**val** 같은 경우는 람다 안에 해당 변수를 복사해서 사용하고 **var**같은 경우 해당 변수의 참조 값을 **final**로 저장하고 그 값을 참조하여 실제 값을 변경할 수 있다.

만약 람다 안에 있는 코드가 실제 함수보다 늦게 끝나게 된다면 람다 안의 변경된 값을 감지 못할 수도 있다. **var** 변수를 람다 안에서 사용하는 경우 함수 밖에 선언해두고 사용하는 것이 좋다.

<br>

## 1.5 멤버 참조

코드 블록을 함수의 인자로 넘길 때 람다를 사용하였다. 만약, 이 코드 블록이 이미 함수로 선언된 경우는 어떻게 해야할까?

이런 경우, 클래스의 멤버(프로퍼티나 메소드)를 참조한다고 하여 **멤버 참조**라는 문법을 사용할 수 있다.

```kotlin
val getAge = Person::age
```

<br>

```kotlin
val people.maxBy{ p -> p.age }
```

위의 코드를 멤버 참조로 변경하면 다음과 같이 변경할 수 있다.

```kotlin
val people.maxBy(People::age)
```


<br>

최상위에 선언된(그리고 다른 클래스의 멤버가 아닌) 함수나 프로퍼티를  참조할 수 있다.

```kotlin
fun salute() = println("Salute!")

fun main(args:Array<String>) {
  run(::salute)
}
```

<br>

확장 함수도 멤버 함수와 똑같은 방식으로 참조할 수 있다.

```kotlin
fun Person.isAdult() = age >= 21
val predicate = Person::isAdult
```

## 2 컬렉션 함수형 API

### 2.1 필수적인 함수: filter와 map

filter 함수는 람다가 true를 반환하는 원소만 모은다.

```kotlin
val list = listOf(1, 2, 3, 4)
println(list.filter { it % 2 == 0 })

[2, 4]
```

<br>

filter 함수는 해당 원소를 변경할 수는 없다. 만약 해당 원소를 변경하고 싶은 경우에는 **map**을 사용해야한다.

```kotlin
val list = listOf(1, 2, 3, 4)
println(list.map{ it * it })
```

<br>

map을 이용하면 Person List로부터 name List를 가져올 수 있다.

```kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
println(people.map { it.name })

[Alice, Bob]
```

<br>

위 예제를 멤버 참조를 이용해서 작성하면 다음과 같다.

```kotlin
people.map(People::name)
```

<br>

filter와 map을 혼용해서 사용하면 다음과 같이 작성할 수 있다.

```kotlin
people.filter { it.age > 30 }
      .map(People::namge)
```

<br>

map의 경우 별도의 함수로 처리한다.

- **키** : filterKeys, mapKeys
- **값** : filterValues, mapValues 

### 2.2 all, any, count, find: 컬렉션에 술어 적용

컬렉션의 모든 원소가 어떤 조건을 만족하는지 판단하는 연산이 있다.

- all
- any
- count
- find

<br>

어떤 사람의 나이가 27살 이하인지 판단하는 술어 함수가 있다.

```kotlin
val canBeInClub27 = { p: Person -> p.age <= 27 }
```

모든 원소가 이 술어를 만족하는지 궁금하다면 **all** 함수를 사용한다.

```kotlin
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.all(canBeInClub27))

>> false
```

술어를 만족하는 원소가 하나라도 있는지 궁금하면 any를 쓴다.

```kotlin
println(people.any(canBeInClub27))


>> true
```

<br>

!all은 any 연산으로 !any는 all 연산으로 구할 수 있다. 그러므로 !all이나 !any는 사용하지 않는 것이 좋다.

```kotlin
val list = listOf(1, 2, 3)
println(!list.all { it == 3 })
println(list.any { it != 3 })
```

<br>

술어를 만족하는 우너소의 개수를 구하려면 count를 사용한다.

```kotlin
val people listOf(Person("Allic", 27), Person("Bob", 31))
println(people.count(canBeInClub27))

1
```

<br>

술어를 만족하는 원소를 하나 찾고 싶으면 find 함수를 사용한다.

```kotlin
val people = listOf(Person("Alice", 27), Person("Bob", 31))
println(people.find(canBeInClub27))

> Person(name=Alice, age=27)
```

find는 만족하는 원소가 없는 경우 null을 반환한다. 만약, null이 나온다는 사실을 명시하고 싶은 경우에는 findOrNull을 사용하면 된다.

<br>

### 2.3 groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경

groupBy를 사용하면 특성에 따라 map이 형성된다.

```kotlin
val people = listOf(Person("Alice", 31),
                    Person("Carol", 31),
                    Person("Bob", 29)
)
println(people.groupBy { it.age })

{
    29=[Person(name=Bob, age=29)],
    31=[Person(name=Alice, age=31), Person(name=Carol, age=31)]
}
```

groupBy의 결과값은 **Map<Int, List\<Person>>** 이 된다. 필요하면 이 맵을 mapKeys나 mapValues 등을 사용해서 변경할 수 있다.

<br>

first는 확장함수이지만 아래와 같이 응용할 수 있다.

```kotlin
val list = listOf("a", "ab", "b")
println(list.groupBy(String::first))

{a=[a, ab], b=[b]}
```

<br>

### 2.4 flatMap과 flatten: 중첩된 컬렉션 안의 원소 처리

책마다 저자가 한 명 또는 여러 명 있다. 도서관에 있는 책의 저자를 모두 모은 집합을 다음과 같이 가져올 수 있다.

```kotlin
class Book(val title:String, val authors: List<String>)

books.flatMap { it.authors }.toSet()
```

<br>

flatMap의 과정은 다음과 같다.

- map : 인자로 주어진 람다를 컬렉션의 모든 객체에 적용한다.
- flatten : 람다를 적용한 결과 얻어지는 여러 리스트를 한 리스트로 모은다.

```kotlin
val strings = listOf("abc", "def")
println(strings.flatMap{ it.toList() })
[a, b, c, d, e, f]
```

<br>

flatMap은 중복을 없앤다.

```kotlin
val books = listOf(
    Book("Thursday Nex", listOf("Jasper Fforde")),
    Book("Mort", listOf("Terry Pratchett")),
    Book("Good Omens", listOf("Terry Practchett", "Neil Gaiman"))
)

println(books.flatMap{ it.authors }.toSet())

>> [Jasper Eforde, Terry Practchett, Neil Gaiman]
```

<br>

## 3. 지연 계산(lazy) 컬렉션 연산

앞에 map이나 filter 같은 몇 가지 컬렉션 함수를 실행하면 결과 컬렉션이 즉시 생성하는 것을 알 수 있다.

이는 컬렉션 함수를 연쇄하면 매 단계마다 계산 중간 결과를 새로운 컬렉션에 임시로 저장한다는 의미이다.

```kotlin
people.map(Person::name).filter { it.startsWith("A) }
```

이를 효율적으로 작업하기 위해서는 **시퀀스**를 이용하면 된다.

```kotlin
people.asSequence() // 원본 컬렉션을 시퀀스로 변환한다.
      .map(Person::name)              // 시퀀스도 컬렉션과 똑같은
      .filter { it.startsWith("A") }  // API를 제공한다.
      .toList() // 결과 시퀀스를 다시 리스트로 변환한다.
```

위와 같이 작성하게 되면 중간 컬렉션이 생기지 않기 때문에 성능이 향상된다.

<br>

### 3.1 시퀀스 연산 실행: 중간 연산과 최종 연산

시퀀스에 대한 연산은 **중간 연산**과 **최종 연산**으로 분류한다. 중간 연산은 다른 시퀀스를 반환하고 최종 연산은 결과를 반환한다.

즉시 계산과 지연 계산의 차이점을 이해하기 위해서는 **중간 연산**과 **최종 연산**의 의미를 이해야한다.

즉시 계산은 결과 값을 반환하기 때문에 collection을 반환한다.

```kotlin
val Immediately = listOf(1, 2, 3, 4).map { it * it }   // [1, 4, 9, 16] 결과값 반환
                                    .find { it > 3 }   // [4] 3이상인 4를 반환한다.

println(Immediately)
```

지연 계산의 경우 다음과 같이 절차가 진행된다.

```kotlin
val lazy = listOf(1, 2, 3, 4).asSequence() //  1 회전   2 회전
                .map { it * it }           //    1       4
                .find { it > 3 }           //            4
                                           //  4는 3보다 크기 때문에 4를 반환한다.
println(lazy)                
```

둘의 차이는 즉시 계산의 경우 컬렉션을 하면서 loop가 돌고 지연 계산의 경우 하나의 요소마다 함수가 실행 된다. 그렇기 때문에 위와 같은 경우는 지연 계산이 효율적이다.

<br>

### 3.2 시퀀스 만들기

시퀀스를 **generateSequence** 함수를 통해서 만들 수 있다.

```kotlin
val naturalNumbers = generateSequence(0) { it + 1 }
val numbersTo100 = naturalNumber.takeWhile { it <= 100 }
println(numberTo100.sum())

>> 5050
```

이 예제에서 naturalNumbers와 numbersTo100은 모두  시퀀스며, 연산은 지연 계산한다. 최종 연산은 수행하기 전까지는 시퀀스의 각 숫자는 계산되지 않는다.

여기서 최종 연산은 **sum**이다.

어떤 객체의 조상이 자신과 같은 타입이고 모든 조상의 시퀀스에서 어떤 특성을 알고 싶을 때 유용하게 사용할 수 있다.

다음 예제는 어떤 파일의 상위 디렉터리를 뒤지면서 숨김 속성을 가진 디렉터리가 있는지 검사함으로써 파일이 감춰진 디렉터리 안에 들어있는지 알아본다.

```kotlin
fun File.isInsideHiddenDirectory() = generateSequence(this) { it.parentFile }.any { it.isHidden }

val file = File("/Users/svtk/.HiddenDir/a.txt")
println(file.isInsideHiddenDirectory())

>> true
```

