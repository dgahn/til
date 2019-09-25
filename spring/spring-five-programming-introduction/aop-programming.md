# AOP 프로그래밍

## Overview

AOP는 **Aspect Oriented Programming**의 약자로 **관점 지향 프로그래밍**이라고 부른다. 핵심은 **핵심 기능 관점**과 **공통 기능 관점**을 분리하여 해당 기능들을 모듈화하는 것이다. 그렇게 되면 **핵심 기능**을 수정할 때는 **공통 기능**의 코드를 수정할 필요 없게 되고 **공통 기능**을 수정할 때는 **핵심 기능**의 코드를 수정할 필요가 없다.

<br>

## 1. AOP의 주요 용어

AOP에서 공통기능을 **Aspect**라고 한다. 그 외에도 아래와 같은 용어들이 있다.

|    용어     | 의미                                                                                                          |
| :-------: | :---------------------------------------------------------------------------------------------------------- |
|  Advice   | 언제 공통 기능을 핵심 기능에 적용할지 분류하는 기준이다. 여러가지 방식이 있는데 주로 실행 전, 후 또는 익셉션 발생 시점에 공통 기능을 실행하는 **Around Advice**를 사용한다. |
| Joinpoint | 공통 기능을 적용할 수 있는 지점을 의미한다. 스프링에서는 메소드 호출에 의해서만 공통기능이 호출된다.                                                   |
| Pointcut  | 실제 공통 기능이 적용되는 Joinpoint를 의미한다.                                                                             |
|  Aspect   | 여러 객체에 적용되는 공통 기능을 의미한다.                                                                                    |

## 2. 스프링 AOP

스프링에서는 AOP를 제공하는 방법은 아래와 같다. 

- 스프링이 **프록시 객체**를 생성한다.
- **프록시 객체**는 **공통 기능**을 담당하는 모듈과 **핵심 기능**을 담당하는 실제 객체를 모두 호출할 수 있다.
- **프록시 객체**가 **핵심 기능** 기능과 **공통 기능**을 적절한 절차에 맞춰 호출한다.

### 2.1 @Aspect, @Pointcut, @Around를 이용한 AOP 구현

먼저, 공통 기능을 담당하는 **LogAspect**를 작성해보자.
```java
@Component
@Aspect // 공통 기능
public class LogAspect {

  @Pointcut("execution(public * spring..*(..))")
  private void publicTarget() {
  }

  @Around("publicTarget()")
  public Object measure(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
    try {
      System.out.println("start");
      return proceedingJoinPoint.proceed();
    } finally {
      System.out.println("end");
    }
  }

}
```

- **@Component** : 등록하기 위해 추가한다.
- **@Aspect** : **공통 기능**을 담당하는 **Aspect** 클래스라는 것을 선언하기 위해 추가한다.
- **@Pointcut** : **공통 기능**을 적용할 지점을 선택한다. 스프링의 경우 메소드를 호출할 때만 **공통 기능**을 적용할 수 있다. 그래서 속성 값으로 어떤 메소드가 호출 됬을 때, 공통 기능을 적용할지를 **excution 명시자 표현식**으로 나타낸다.
- **@Around** : **Around Advice**를 의미한다. **Around Advice**는 **Pointcut**에 의해 지정된 지점에서 메소드를 실행 전, 실행 후, 익셉션이 발생 될 때, **공통 기능**을 실행하도록 한다.
- **ProceedingJoinPoint** : 이 클래스는 실제 객체의 메소드를 호출할 때 사용한다. **proceedingJoinPoint.proceed()** 메소드를 호출하면 실제 **Pointcut**에 존재하는 **핵심 기능**의 메소드를 호출한다.

<br>

```java
@Configuration
@ComponentScan(basePackages = {"spring"})
@EnableAspectJAutoProxy
public class AppContext {

  @Bean
  public Mouse mouse() {
    return new Mouse();
  }

  @Bean
  public Keyboard keyboard() {
    return new Keyboard();
  }

  @Bean
  public Computer computer() {
    return new Computer(keyboard());
  }

}
```

- **@EnbleAspectJAutoProxy** : 스프링 설정 클래스에 추가하는 애노테이션으로 **@Apect** 애노테이션을 설정한 빈 객체를 찾아서 **@Pointcut**과 **@Around** 설정을 사용한다. 