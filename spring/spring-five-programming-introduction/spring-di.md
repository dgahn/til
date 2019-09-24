# 스프링 DI

## 1. 의존이란?

우리가 클래스를 선언할 때, 속성으로 여러가지 타입을 선언할 수 있다 아래의 예시를 보자.

```java
public class Mouse {

    public void click() {
        System.out.println("click called");
    }

}

public class Computer
 {

    private Mouse mouse;

    public Computer() {
        mouse = new Mouse();
    }

    public void mouseClick() {
        mouse.click();
    }

}

```

<br>

**Computer** 클래스가 있고 **Computer** 클래스에는 **Mouse** 타입의 변수가 있다. **Mouse**의 메소드인 **click()** 의 이름이 **doublClick()** 로 바뀌면 **Computer**의 메소드 **mouseClick()**에서 **click()** 메소드를 호출하는 부분을 **doublClick()** 을 호출하도록 변경해야할 것이다. 이처럼 **Mouse** 클래스가 변화하였을 때, **Computer** 클래스에 영향을 미치면 **Computer** 클래스는 **Mouse** 클래스에 `의존`한다고 표현한다.

<br>

## 2. DI를 통한 의존 처리

아래의 코드 같은 경우에는 **Computer** 라는 클래스의 **mouse** 변수를 할당할 때 직접 객체를 생성한다.

```java
public class Computer
 {

    private Mouse mouse;

    public Computer() {
        mouse = new Mouse();
    }

 }
```

<br>

만약, **Mouse**의 기능이 확장된 **NewMouse**라는 객체를 **Computer**에서 사용하고 싶다면 아마 아래와 같이 코드를 변경할 것이다.

```java
public class NewMouse extends Mouse {

}
```

```java
public class Computer
 {

    private Mouse mouse;

    public Computer() {
        mouse = new NewMouse();
    }

 }
```

**Computer** 클래스의 코드를 직접 변경했다. 만약, 이렇게 **Mouse**를 **NewMouse**로 변경해야하는 일이 많다면 해당 클래스의 코드를 모두 바꿔야 하는 불편함이 있을 것이다.

아래의 예제를 보자.

```java
public class Computer
 {

    private Mouse mouse;

    public Computer(Mouse mouse) {
        this.mouse = mouse;
    }

 }
```

**Computer** 클래스를 생성할 때, **Mouse** 클래스 대신 **NewMouse**를 생성자의 파라미터로 넘기면 **Computer** 클래스의 코드는 변경될 일이 없다. 이처럼 **Computer** 클래스 외부로부터 **Mouse** 클래스를 주입 받는 방식을 `DI(Dependency Injection, 의존주입)`이라고 표현한다.

## 3. 객체 조립기

우리가 객체를 주입하기 위해서는 어디선가는 그 객체를 생성해서 넣어줘야 한다. 아래와 같이 메인 메소드에서 객체를 생성해도 된다.

```java
public class Main {

    public static void main(String[] args) {
      Mouse mouse = new Mouse();  
      Computer computer = new Computer(mouse);
    }

}
```

또 다른 방법으로는 의존 객체를 생성해주는 별도의 클래스를 만드는 것이다.

```java
public class Assembler {

    private Mouse mouse;
    private Computer computer;

    public Assembler() {
        this.mouse = new Mouse();
        this.computer = new Computer(mouse);
    }

    public Mouse getMouse() {
        return mouse;
    }

    public Computer getComputer() {
        return computer;
    }

}
```

이와 같은 클래스를 **Assembler** 또는 **객체 조립기**라고 표현한다. 그러면 우리는 **Assembler**를 통해서 아래와 같이 생성된 객체를 가져와 사용하면 된다.

```java
public class Main {

    public static void main(String[] args) {
      Assembler assembler = new Assembler();  

      Mouse mouse = assembler.getMouse()
      Computer computer = assembler.getComputer();
    }

}
```

## 4. 스프링의 DI 설정

앞에서 **의존**, **DI** 그리고 **Assembler**에 대해서 살펴보았다. 이 3개에 대해서 살펴본 이유는 스프링이 **DI**를 제공해주는 **Assembler**이기 때문이다.

스프링에게 객체 조립을 맡기기 위해서는 설정 코드를 작성해야한다.

```java
@Configuration
public class AppContext {

    @Bean
    public Mouse mouse() {
        return new Mouse();
    }

    @Bean
    public Computer computer() {
        return new Computer(mouse());
    }

}
```

- **@Configuration** : 스프링에게 해당 클래스가 **스프링 설정 클래스**라는 것을 알려주는 애노테이션이다.
- **@Bean** : 해당 메소드가 리턴하는 객체를 빈 객체(스프링이 관리하는 객체)가 되도록 선언한다. 별다른 설정을 하지 않으면 객체가 **싱글톤**으로 유지된다. 주의할 점은 스프링에 해당 객체가 **메소드 이름**으로 등록된다는 점이다.

<br>

 ```java
public class Main {

    public static void main(String[] args) {
      ApplicationContext appContext = new AnnotationConfigApplicatoinContext(AppContext.class);

      Mouse mouse = appContext.getBean("mouse", Mouse.class);
      Computer computer = appContext.getBean("computer", Computer.class);
    }

}
```

- **ApplicationContext** : 스프링 설정의 중심이 되는 인터페이스이다. 여러가지 구현체가 있는데 설정 파일을 대표적으로 설정 파일이 무엇이냐에 따라 구현체가 달라지기도 한다. 예시 구현체는 아래와 같다.
  - **AnnotationConfigApplicationContext** : Java 애노테이션을 사용한 클래스로부터 객체 설정 정보를 가져온다.
  - **GenericXmlApplicationContext** : XML로부터 객체 설정 정보를 가져온다.
  - **GenericGroovyApplicationContext** : Groovy 코드를 이용한 설정 정보를 가져온다.

### 4.1 생성자 방식과 Setter 방식

스피링을 통해서 빈 객체로 주입하는 방법은 두가지가 있다. 

- 생성자 방식

```java
@Configuration
public class AppContext {

    @Bean
    public Mouse mouse() {
        return new Mouse();
    }

    @Bean
    public Computer computer() {
        return new Computer(mouse());
    }

}
```

위와 같이 생성자를 통해서 객체를 주입할 수 있다.

<br>

- Setter 방식

```java
@Configuration
public class AppContext {

    @Bean
    public Mouse mouse() {
        return new Mouse();
    }

    @Bean
    public Computer computer() {
        Computer computer = new Computer()
        computer.setMouse(mouse());
        return computer;
    }

}
```

위와 같이 Setter 메소드를 통해서 객체를 주입할 수 있다.

> 생성자 주입 방식과 Setter 주입 방식의 차이점  
> 생성자 주입 방식 : 빈 객체를 생성하는 시점에 모든 의존 객체를 넣을 수 있다. 하지만, 의존 객체가 너무 많으면 어떤 객체를 주입해야하는지를 알기 위해 생성자 코드를 봐야한다.
> Setter 주입 방식 : Setter 메소드를 통해서 어떤 객체가 주입되는지 명확하게 알 수 있다. 하지만, 의존 객체를 Setter 메소드를 통해 주입하지 않고 의존 객체를 호출하는 경우에 NullPoint Exception이 발생할 수 있다.

## 여러 설정파일 사용하는 방법



- **@Import** : 이 애노테이션을 통해서 다른 스프링 설정파일을 해당 스프링 설정파일에 포함한다. 
  - 포함해야하는 스프링 설정 파일 하나인 경우
    ```java
    @Configuration
    @Import(AppDbContext.class)
    public class AppContext {
      
    }
    ```
  - 포함해야하는 스프링 설정 파일이 여러개인 경우
    ```java
    @Configuration
    @Import({AppDbContext.class, AppWebContext.class})
    public class AppContext {

    }
    ```

## 용어 정리

- **의존** : A 클래스가 변경됬을 때, A 객체를 사용하고 있는 B 클래스가 영향을 받을 때, B 클래스는 A 클래스에 의존한다고 표현한다.  
- **DI(Dependency Injection)** : 의존 객체를 외부에서 주입받는 방식을 말한다.  
- **Bean** : 스프링 컨테이너에 의해 관리되고 있는 객체를 말한다.
- **스프링 컨테이너**: 웹 애플리케이션에서 사용할 객체를 관리해준다. **ApplicationContext**가 이 역할을 해준다.
- **스프링 IoC**: 스프링을 사용하게 되면 객체의 생명 주기를 컨테이너에게 넘겨주게 되어 응용프로그래머가 제어할 수 없게 된다. 이를 **Inversion of Control**, 제어의 역전이라고 부른다.
