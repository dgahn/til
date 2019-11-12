# Inheritance(상속)

## 1. 코틀린에서의 상속

코틀린의 최상위 클래스는 Any이다. 그래서 클래스에 상위타입을 선언하지 않으면 Any가 상속된다.

```kotlin
class Example // 암시적으로 Any를 상속한다.
class Example : Any() // 명시적으로 Any를 상속한다.
```

Any는 java.lang.Object와는 다른 클래스이다.
- equals(), hashCode(), toString()만 있다.
```kotlin
package kotlin
public open class Any {
    public open operator fun equals(other: Any?): Boolean
    public open fun hashCode(): Int
    public open fun toString(): String
}
```

<br>

```kotlin
open class Base(p: Int)

class Derived(p: Int): Base(p)
```

코틀린에서 명시적으로 상위타입을 선언하려면, 클래스 헤더의 콜론(:) 뒤에 상위 타입을 선언해야한다.

파생클래스에 기본생성자가 있으면, 파생클래스의 기본생성자에서 상위 타입의 생성자를 호출해서 초기화할 수 있다.

```kotlin
class MyView: View {
    constructor(): super(1)

    constructor(ctx: Int): this()

    constructor(ctx: Int, attrs: Int): super(ctx, attrs)
}
```

파생 클래스에 기본 생성자가 없으면 각가의 보조 생성자에서 상위 타입을 super 키워드를 통해서 초기화 해줘야한다.

아니면 다른 생성자에게 상위타입을 초기화할 수 있게 위임해줘야한다.

open 어노테이션은 Jav의 fianl과 반대이다.

open class는 다른 클래스가 상속할 수 있다.

기본적으로 코틀린의 모두 class는 final이다.

<br>

## 2. 메소드 오버라이딩

```kotlin
open class Base {
    open fun v() {}
    fun nv() {}
}

class Derived(): Base() {
    override fun v() {}
}
```

오버라이딩이 될 메소드에 **open** 어노테이션이 요구된다. 그리고 오버라이딩이 된 메소드에 **override** 어노테이션이 요구된다.

<br>

## 3. 프로퍼티 오버라이딩

```kotlin
open class Foo {
    open val x: Int get {}
}

class Bar1: Foo() {
    override val x: Int = ...
}
```

메소드 오버라이딩과 유사한 방식으로 프로퍼티를 오버라이딩 할 수 있다.

<br>

## 4. 오버라이딩 규칙

같은 멤버에 대한 중복된 구현을 상속 받은 경우, 상속 받은 클래스는 해당 멤버를 오버라이딩하고 자체 구현을 제공해야한다.

이를 위해서 super + <클래스명>을 통해서 상위 클래스를 호출할 수 있다.

```kotlin
open class A {
    open fun f() {print("A")}
    fun a() {print("a")}
}
```

```kotlin
interface B {
    fun f() {print("B")}
    fun b() {print("b")}
}
```

```kotlin
class C(): A(), B {
    override fun f() {
        super<A>.f()
        super<B>.f()
    }

}
```

## 5. 추상 클래스가

abstract 멤버는 구현이 없다. abstract 클래스나 멤버는 open이 필요 없다.

```kotlin
abstract class AbsClass {
    abstract fun f()
}

class MyClass(): AbsClass() {
    override fun f() {
        /* */
    }
}
```

