# 스프링 IoC와 DI

## 1. IoC와 DI의 관계

스프링 **IoC(Inversion of Control)** 의 주목적은 의존 객체가 필요로 하는 의존성의 라이프사이클 전반을 스프링이 한다는 의미이다. 일반적으로 스프링에서 **IoC**를 통해서 의존성을 제공해주는 방법은 **DI**이다. 하지만, 반드시 **DI**를 통해서만 의존성을 제공할 수 있는 것은 아니다.

**IoC**가 의존성을 제공해주는 방법은 아래와 같다.

- Dependency Lookup
  - Dependency Pull
  - Contextualized Dependency Lookup

- Dependency Injection
  - Constructor Dependency Injection
  - Setter Dependency Injection

<br>

### 1.1 Dependency Lookup

**의존성 룩업**이라고 번역한다. 의존성 룩업은 응용프로그래머가 필요한 의존 객체에 대해서 필요할 때, 가져오는 방식을 말한다.

<br>

#### 1.1.1 Dependency Pull

의존성 룩업의 첫 번째 방식인 의존성 풀은 의존성을 생성하는 컨테이너가 **레지스트리**에 의존성을 저장하고 컨테이너의 메소드를 통해서 의존성을 가져오는 방식이다.

스프링에서는 **Applicationcontext**이 **IoC 컨테이너**이자 **의존성 풀**로 보인다. (아직 정확하지 않다.)

아래 코드를 보자.

```java
public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppContext.class);
    
    Computer computer = ctx.getBean("computer", Computer.class);
}
```

코드를 보면 메인 메소드를 실행하면 **Applicationcontext**을 통해서 **Computer** 빈 객체를 검색해서 의존성을 가져온다.

<br>

#### 1.1.2 Contextualized Dependency Lookup

번역하면 문맥에 따른 의존성 룩업이다. 즉, 몇 가지 정해진 시점에 따라 의존성을 검색하여 가져온다는 의미이다. 스프링에서는 **ApplicationContextAware**을 구현하여 **CDL**을 수행할 수 있다.

<br>

```java
public class Computer implements ApplicationContextAware {

  private Keyboard keyboard;

  @Override
  public void setApplicationContext(final ApplicationContext applicationContext) throws BeansException {
    this.keyboard = applicationContext.getBean("keyboard", Keyboard.class);
  }

}
```

**Computer** 클래스는 **Keyboard** 객체를 직접 만들지 않고 **ApplicationContextAware**를 통해 구현한 메소드인 **setApplicationContext()** 메소드를 통해서 **ApplicationContext**를 받아서 **Keyboard** 빈 객체를 **Dependeny Lookup**한다.

<br>

#### 1.1.3 Dependency Pull와 Contextualized Dependency Lookup의 차이

**Dependency Pull**은 아래 그림과 같이 컨테이너가 의존 객체가 필요로하는 의존성을 중앙 레지스트리에 저장을 하면 그것을 의존 객체가 **중앙 레지스트리에서 가져가는 형태**이다.  
<img src="https://imgur.com/BCZ8syM.png" width="600" >

하지만 **Contextualized Dependency Lookup** 같은 경우는 컨테이너가 의존 객체가 필요로 하는 의존성을 가지고 있어 **컨테이너를 통해서 의존성**을 가져가는 형태이다.
추가적으로 **CDL**의 경우 몇 가지 정해진 시점에서 의존성을 가져간다는 것이 특징이다.   
<img src="https://imgur.com/oy7swo6.png" width="430" >

스프링에서는 컨테이너와 의존성 풀이 **ApplicationContext**로 동일시 되기 때문에 그 차이는 정해진 시점에 따라 의존성을 가져간다는 것에서 차이가 있는 것으로 보인다.

<br>

### 1.2 Dependency Injection

의존성 주입에 대해서는 [스프링 의존 자동 주입](../spring-five-programming-introduction/dependent-automatic-injection.md)를 참고하면 된다.

