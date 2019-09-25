# 빈 라이프사이클과 범위

빈은 스프링 컨테이너에 의해서 관리된다. 빈의 라이프사이클을 알아보기 전에 스프링 컨테이너의 라이프 사이클을 먼저, 알아보도록 하자.

## 1. 스프링 컨테이너 라이프사이클

**ApplicationContext**에 의해 생성된 스프링 컨테이너의 라이프 사이클은 아래와 같다.

#### 1. 스프링 컨테이너 생성 및 초기화
#### 2. 스프링 컨테이너 사용
#### 3. 스프링 컨테이너 종료

코드로 확인 하면 아래와 같다.

```java
public static void main(String[] args) {
    // 스프링 컨테이너 생성 및 초기화
    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppContext.class);
    
    // 스프링 컨테이너의 사용
    Computer computer = ctx.getBean("computer", Computer.class);
    
    // 스프링 컨테이너의 종료
    ctx.close();
}
```

<br>

## 2. 스프링 빈 객체의 라이프 사이클

스프링 컨테이너가 생성되고 초기화될 때, 스프링 빈도 같이 생성되고 초기화 된다. 그리고 스프링 컨테이너가 완전히 소멸되기 전에 빈 객체가 소멸되고 스프링 컨테이너가 종료된다. 빈 객체의 라이프 사이클은 아래와 같다.

#### 1. 객체 생성
#### 2. 의존 설정
#### 3. 초기화
#### 4. 소멸

빈 객체가 생성되고 의존 설정에 따라서 의존 객체가 주입이 된다. 그러면 빈은 메모리 상에 인스턴스화가 된다. 그리고 초기화 과정을 거치는데 초기화 과정을 넣는 이유는 빈이 생성되고 나서 응용프로그래머가 어떤 작업을 추가적으로 할 수 있게 하기 위함이다. 


### 2.1 스프링 빈 객체의 초기화
스프링 빈이 초기화를 활용하는 방법은 아래의 3가지가 있다.

- 빈 객체 안의 **@PostConstruct**을 선언한 메소드 활용
- **InitializingBean** 인터페이스의 **afterPropertiesSet()** 메소드 활용
- **@Bean** 애노테이션의 **initmethod** 속성 활용

> 3가지 방법을 모두 동시에 사용할 수 있는데 실행 순서는 위에 나열한 순서이다.

<br>

빈 초기화의 활용을 코드로 보면 아래와 같다.

```java
public class Keyboard implements InitializingBean {

  public void sound() {
    System.out.println("타자타자");
  }

  @PostConstruct // @PostConstruct을 선언한 메소드 활용
  public void postConstruct() {
    System.out.println("postConstruct called");
  }

  @Override // InitializingBean 인터페이스의 afterPropertiesSet() 메소드 활용
  public void afterPropertiesSet() throws Exception {
    System.out.println("afterPropertiesSet called");
  }

  private void initMethod() { // @Bean 애노테이션의 initmethod 속성 활용
    System.out.println("initMethod called");
  }

}
```

```java
public class AppContext {

  @Bean(initMethod = "initMethod") // @Bean 애노테이션의 initmethod 속성 활용
  public Computer computer() {
    return new Computer(keyboard());
  }

}
```

<br>

### 2.3 스프링 빈 객체의 소멸

스프링 컨테이너가 종료되기 전에 스프링 컨테이너는 만들어져 있는 스프링 빈 객체를 먼저 소멸시키고 종료한다. 소멸 되는 시점에도 응용프로그래머가 빈 객체의 소멸 과정에 원하는 로직을 넣을 수 있다. 

스프링 빈이 초기화를 활용하는 방법은 아래의 3가지가 있다.

- 빈 객체 안의 **@PreDestroy**을 선언한 메소드 활용
- **DisposableBean** 인터페이스의 **destroy()** 메소드 활용
- **@Bean** 애노테이션의 **destroyMethod** 속성 활용

> 3가지 방법을 모두 동시에 사용할 수 있는데 실행 순서는 위에 나열한 순서이다.

<br>

빈 소멸의 활용을 코드로 보면 아래와 같다.

```java
public class Mouse implements DisposableBean {

  @PreDestroy // @PreDestroy을 선언한 메소드 활용
  public void preDestroy() {
    System.out.println("preDestroy called");
  }

  @Override // DisposableBean 인터페이스의 destroy() 메소드 활용
  public void destroy() throws Exception {
    System.out.println("destroy called");
  }

  // @Bean 애노테이션의 destroyMethod 속성 활용
  private void destroyMethod() {
    System.out.println("destroyMethod called");
  }

}
```

```java
public class AppContext {

  @Bean(destroyMethod = "destroyMethod") // @Bean 애노테이션의 destroyMethod 속성 활용
  public Mouse mouse() {
    return new Mouse();
  }

}
```

## 3. Bean Scope

스프링은 빈을 생성할 때, 기본적으로 하나의 객체만 생성한다. 즉, **싱글톤** 객체를 생성한다. 

```java
Mouse mouse1 = ctx.getBean("mouse", Mouse.class);
Mouse mouse2 = ctx.getBean("mouse", Mouse.class);
// mouse1 == mouse2 -> true
```

필요에 따라서 이 객체를 **싱글톤** 객체가 아니라 새로운 객체로 생성할 수 있는데 이를 **Scope**를 지정한다고 말한다.

### 3.1 @Scope

**@Scope**는 두가지로 활용된다.

- **@Scope("singleton")** : 빈의 Scope를 싱글톤으로 설정한다. 그러므로 스프링은 해당 빈을 하나의 객체로만 유지한다.
- **@Scope("prototype")** : 빈의 Scope를 프로토타입으로 설정한다. 스프링은 해당 빈을 호출할 때마다 새로운 객체를 생성한다.

```java
public class AppContext {

  @Bean
  @Scope("singleton") // 생략 가능하다.
  public Mouse mouse() {
    return new Mouse();
  }

  @Bean
  @Scope("prototype")
  public Keyboard keyboard() {
    return new Keyboard();
  }

}
```

<br>

## 용어 정리

- **@PostConstruct** : 빈 객체가 초기화할 때, 추가적인 설정이 있는 경우 메소드에 적용해서 사용할 수 있는 애노테이션이다.
- **InitializingBean** : 빈 객체가 초기화할 때, 추가적인 설정을 할 수 있는 메소드인 **afterPropertiesSet()** 을 가지고 있는 인터페이스이다.
- **initmethod** : 빈 객체가 초기화할 때, 추가적인 설정을 할 수 있는 커스텀 메소드를 지정할 수 있는 **@Bean**의 속성이다.
- **@PreDestroy** : 빈 객체가 소멸할 때, 추가적인 설정이 있는 경우 메소드에 적용해서 사용할 수 있는 애노테이션이다.
- **DisposableBean** : 빈 객체가 소멸할 때, 추가적인 설정을 할 수 있는 메소드인 **destroy()** 을 가지고 있는 인터페이스이다.
- **destroyMethod** : 빈 객체가 소멸할 때, 추가적인 설정을 할 수 있는 커스텀 메소드를 지정할 수 있는 **@Bean**의 속성이다.
- **@Scope** : 빈 객체가 메모리상에 존재할 수 있는 인스턴스의 갯수를 지정할 수 있는 애노테이션이다. 하나만 유지하고 싶은 경우는 **"singleton"** (생략 가능), 매번 생성하도록 할 경우에는 **"prototype"** 을 활용한다.