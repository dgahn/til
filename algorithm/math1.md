# 수학 1

## 나머지 연산

- (A + B) % M = ((A%M) + (B%M)) % M
- (A X B) % M = ((A%M) X (B%M)) % M

위의 두가지가 설립된다. 하지만 뺄셈의 경우 mod 연산을 한 결과가 음수가 나올 수 있기 때문에 아래와 같이 해야한다.

- (A-B) % M = ((A % M) - (B % M) + M) % M

## 최대공약수

최대공약수는 A와 B의 공통된 약수 중에서 가장 큰 정수이다.

유클리드 호제법을 이용하는 방법인데 아래와 같다.


```
A를 B로 나눈 나머지를 R이라고 할 때, R이 0이 될 때까지 아래와 같이 연산한다.

A % B = R
A = B
B = R

R = 0이 나왔을 때, 그 때의 A가 최대공약수이다.
```

코드로 표현하면 아래와 같다.

```java
int gcd(int x, int y) {
    while(y != 0) {
        int r = x % y;
        x = y;
        y = r;
    }
    return x;
}
```

## 최소공배수

최소공배수의 정의는 숫자 A와 B가 있을 때, A X B / 최대공약수이다. 그렇기 때문에 코드로 표현하면 아래와 같다.

```java
int lcm(int x, int y) {
    return x*y/gcd(x, y);
}
```

## 소수

약수가 1과 자기 자신 밖에 없는 수를 의미한다.

#### 어떤 수 N이 소수인지 아닌지 알아보는 방법

어떤 수 N이 있을 때, 2보다 크고 N-1이하보다 작은 수로 나누었을 때 나머지가 0이어야 한다. 코드로 표현하면 아래와 같다.

```java
boolean prime(int N) {
    if (N < 2) {
        return false;
    }

    for(int i = 2; i < N - 1; i++) {
        if(N % i == 0) {
            return false;
        }
    }
    return true;
}
```

시간 복잡도를 줄이기 위해서는 생각의 전환이 필요하다. 어떤 수 N이 있을 때, N이 2 X N / 2라고 하자.

그러면 이 수는 2 부터 N / 2 까지 나머지를 구했을 때, 0이 되야한다. 코드로 표현하면 아래와 같다.

```java
boolean prime(int N) {
    if (N < 2) {
        return false;
    }

    for (int  i = 2; i <= N/2; i++) {
        if (N % i == 0) {
            return false;
        }
    }

    return true;
}
```

가장 빠른 방법은 2부터 루트 N까지의 나머지를 구해보면 된다. 이유는 강의를 참고하자.

```java
boolean prime(int N) {
    if (N < 2) {
        return false;
    }

    for (int  i = 2; i * i <= N; i++) {
        if (N % i == 0) {
            return false;
        }
    }

    return true;
}
```

#### 범위의 수가 소수인지 아닌지 알아보는 방법

에라토스테네스의 체를 이용한다.

```java
int primes(int[] numbers) {
    boolean[] check = new boolean[numbers.length]; // 지워진 숫자에 대해서 true
    int pn = 0; // 소수의 개수

    for(int i = 2; i <= numbers.length; i++) {
        if (check[i] == false) { // 지지워지 않았으면
            numbers[pn++] = i;
            for (int j = i + i; j < = n; j+=i) { 
                check[j] = true;
            }
        }
    }

}
```

## 팩토리얼

팩토리얼의 0이 몇개인지 알아내는 문제가 나온다. 0은 2 X 5로 만들어진다. 그렇기 때문에 소인수 분해를 통해서 2 X 5가 몇개인지 알아보면 된다.

항상 2의 개수보다 5의 개수가 많기 때문에 5의 개수만 세보면 된다. 

```java
int getZeroOfFactorial(int N) {
    int result = 0;
    for(int i = 0; i <= N; i++) {
        for(int j = 5; j <= N; j*=j) {
            if(i % j == 0) {
                result++;
            }
        }
        
    }
}
```

