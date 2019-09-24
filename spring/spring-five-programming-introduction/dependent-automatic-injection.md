# 의존 자동 주입

스프링은 스프링이 자동으로 의존 빈 객체를 주입시키도록 할 수 있는 기능인 **@Autowired**와 **@Resource** 제공한다. 여기서는 **@Autowired**만 살펴보도록 하겠다.

## 1. @Autowired을 이용한 의존 자동 주입

**@Autowired**을 통해서 의존 객체를 주입하는 방법은 3가지가 있다.

- 필드 주입 방식

- Setter 주입 방식

- 생성자 주입 방식

스프링 설정 클래스에 아래와 같이 작성되어 있다고 가정하도록 하겠다.

```java
public class AppContext {

    @Bean
    public KeyBoard keyBoard() {
        return new KeyBoard();
    }

    @Bean
    public Mouse mouse() {
        return new Mouse();
    }

    @Bean
    public Printer printer() {
        return new Printer();
    }

    @Bean
    public Computer computer() {
        return new Computer(printer());
    }

}
```

<br>

### 1.1 필드 주입 방식

필드 주입 방식을 사용하는 방법은 매우 간단하다. 해당 클래스가 스프링 빈 객체로 등록되어있다면 아래와 같이 사용하면 된다.

```java
public class Computer {

    @Autowired
    private Keyboard keyboard;

    private Mouse mouse;

}
```

위와 같이 작성하면 스프링이 클래스 타입이 **Keyboard**인 빈 객체를 찾아서 해당 변수에 의존 객체를 주입시켜준다.

<br>

### 1.2 Setter 주입 방식

Setter 주입 방식도 간단하다. 아래와 같이 작성하면 된다.
```java
public class Computer {

    @Autowired
    private Keyboard keyboard;

    private Mouse mouse;

    @Autowired
    public void setMouse(Mouse mouse) {
        this.mouse = mouse;
    }

}
```

위와 같이 **Setter** 메소드에 **@Autowired**를 추가해주면 된다. 그러면 **Setter 메소드**의 파라미터를 분석하여 원하는 알맞는 의존 객체를 주입시킨다.

<br>

### 1.3 생성자 주입 방식

생성자 주입 방식으로 넣기 위해서는 **@Component**에 대해서 알아야함으로 잠시 미뤄두기로 하겠다.


## 2. @Qualifier를 통해서 의존 객체 선택

아래와 같이 **Mouse** 객체 두 개를 빈으로 등록한 경우 어떻게 구별하여 의존 객체에 넣을까? 

```java
public class AppContext {

    @Bean
    public Mouse mouse() {
        return new Mouse();
    }

    @Bean
    public Mouse mouse1() {
        return new Mouse();
    }

}
```

이에는 몇가지 상황이 있는데 하나씩 살펴보도록 해보자.

<br>

### 2.1 빈 생성 메소드 이름과 주입할 필드 이름이 같은 경우

```java
public class Computer {

    @Autowired
    private Mouse mouse1;

}
```

위와 같이 작성을 하면 **AppContext** 클래스에 있는 **mouse1()** 메소드에 의해 생성된 빈 객체가 의존 객체로 들어간다.

<br>

### 2.2 빈 생성 메소드 이름과 주입할 필드 이름이 다른 경우

```java
public class Computer {

    @Autowired
    @Qualifier("mouse")
    private Mouse mouse2;

}
```

위와 같이 **@Qualifier** 애노테이션을 통해서 주입한 의존 객체의 이름을 명시하면 된다. 다시 말하지만 빈 객체의 이름은 별도로 지정하지 않으면 스프링 설정 클래스인 **AppContext** 클래스의 메소드의 이름이 빈 객체의 이름이 된다.


### 2.3 빈 객체의 이름을 변경하고 싶은 경우

빈 객체의 이름을 변경하고 싶은 경우는 아래와 같이 **AppContext** 클래스의 메소드에 **@Qualifier** 애노테이션을 통해서 이름을 지정하면 된다.

```java
public class AppContext {

    @Bean
    @Qualifier("mouse2")
    public Mouse mouse() {
        return new Mouse();
    }

    @Bean
    public Mouse mouse1() {
        return new Mouse();
    }

}
```

## 3. 상위/하위 타입 관계와 자동 주입

상속에 의한 상위 / 하위 타입 관계의 두 빈 객체가 아래와 같이 스프링 설정 클래스에 등록되어 있다고 하자.


```java
public class AppContext {

    @Bean // 상위 타입
    public Mouse mouse() {
        return new Mouse();
    }

    @Bean // 하위 타입
    public Mouse subMouse() {
        return new SubMouse();
    }

}
```

아래의 클래스에 각각 어떻게 의존 객체를 주입시킬 수 있을까?

```java
public class Computer {

    @Autowired
    private Mouse mouse;

    @Autowired
    private SubMouse subMouse;

}
```

<br>

### 3.1 상위 타입 클래스

상위 타입 클래스인 **Mouse**로 선언된 필드는 **mouse**는 우리가 알고 있는 상속의 특성에 의해서 **mouse** 이름을 가진 빈 객체 밖에 주입을 받을 수 없다. 

<br>

### 3.2 하위 타입 클래스

하위 타입 클래스인 **SubMouse**는 상위 타입과 하위 타입 모두 주입 받을 수 있다. 그러므로 아래와 같이 작성할 수 있다.

```java
public class Computer {

    @Autowired
    private Mouse mouse;

    @Autowired
    @Qualifier("mouse")
    // @Qualifier("subMouse")
    private SubMouse subMouse;

}
```

<br>

## 용어 정리

- **@Autowired** : 스프링이 관리하는 빈 객체를 주입 받기 위해 사용하는 애노테이션
- **@Qualifier** : 스프링이 의존 자동 주입을 할 때, 빈 객체의 이름(한정자)을 지정할 때 사용하는 애노테이션