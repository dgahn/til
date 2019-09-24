# 컴포넌트 스캔

지금까지는 **빈 등록**을 스프링 설정 클래스를 통해서만 했다. **@ComponentScan**과 **@Component**를 사용하면 스프링 설정 클래스에 코드를 작성하지 않아도 스프링이 알아서 빈을 등록하도록 설정할 수 있다.

## 1. @ComponentScan으로 스캔 범위 설정

**@ComponentScan**을 사용하면 스캔 범위를 패키지 단위로 묶을 수 있다.

```java
@Configuration
@ComponentScan(basePackages = {"spring"})
//@ComponentScan(basePackageClasses = {Computer.class})
public class AppContext {
  
}
```

- **basePackages** : 해당 패키지 이하의 모든 **@Component**을 스캔한다.
- **basePackageClasses** : 해당 클래스가 존재하는 패키지 이하의 모든 **@Component**를 스캔한다.

<br>

## 2. @Component로 빈 등록

**@Component**을 사용하면 스프링 설정 클래스에 빈을 등록하지 않아도 빈으로 등록한다고 스프링에게 명시할 수 있다. 단, 해당 클래스가 **@ComponentScan**이 스캔하는 범위의 패키지에 존재해야 한다.

```java
@Component
public class Computer {

}
```

<br>

만약 빈 객체의 이름을 변경하고 싶은 경우, 아래와 같이 **@Component**에 이름을 지정하면 된다.
```java
@Component("mouse1")
public class Mouse {

}
```

<br>

## 3. 기본 스캔 대상

**@ComponentScan**에 의해서 스캔되는 대상은 아래와 같다.

- @Component
- @Controller
- @Service
- @Repository
- @Aspect
- @Configuration

## 4. 생성자 주입 방식으로 의존 자동 주입

생성자 주입 방식으로 의존 객체를 자동 주입하기 위해서는 **@ComponentScan**과 **@Component**을 사용하면 된다.

```java
@Configuration
@ComponentScan(basePackages = {"spring"})
//@ComponentScan(basePackageClasses = {Computer.class})
public class AppContext {
  
}
```

```java
@Component
public class Mouse {

}
```

```java
@Component
public class Computer {

    private Mouse mouse;

    @Autowired
    puiblic Computer(Mouse mouse) {
        this.mouse = mouse;
    } 

}
```

**@Component**를 통해서 빈으로 등록된 **Mouse** 빈을 **Computer** 클래스의 생성자 메소드에 **@Autowired**를 통해서 의존 자동 주입하는 코드이다.

<br>

## ※ 필드 주입 방식을 사용하지 말자.

스프링에서 의존 자동 주입하는 방법은 아래의 3가지 방식이 있다.

- 필드 주입 방식
- 생성자 주입 방식
- Setter 메소드를 통한 주입 방식

그 중 필드 주입 방식을 사용하는 것을 지양하는데 이유는 아래와 같다.

- 사용하기 편하기 때문에 남용할 가능할 가능성이 크다. 남용하게 되면 그만큼 객체의 역할이 커지기 때문에 **SRP**을 위배할 가능성이 크다.
- 단위 테스트를 진행할 때, 필드 주입을 사용하면 Mock 객체를 사용하기 어렵다. 즉, 객체간의 결합도가 높아질 수 있다는 말이다.
- 필드 주입 방식을 사용하면 **final** 키워드를 사용할 수 없다.
- 생성자 주입 방식을 사용하면 **BeanCurrentlyCreationExeption**을 통해서 순환 의존성을 알 수 있다.

<br>

## 용어 정리

- **@Component** : 스프링 설정 클래스에 빈을 등록하지 않고 스프링에게 빈으로 사용할 것을 명시하는 애노테이션이다. 단, **@ComponentScan**의 스캔 범위에 있어야 한다.  
- **@ComponentScan** : 빈 등록을 명시한 클래스는 스캔 하기 위한 범위는 지정 해주는 애노테이션이다.  
- **순환 의존성** : A 클래스가 B 클래스에 의존하고 B 클래스가 C 클래스에 의존하는데 C 클래스가 A 클래스에 의존하면 의존하는 경우를 **순환 의존성**이라고 부른다.  