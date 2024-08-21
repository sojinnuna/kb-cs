# AOP
### Cross-Cutting Concerns (횡단 관심사)
**개념**
- 애플리케이션을 개발하다 보면 비즈니스 로직 외에도 다양한 기능이 필요하다.
- 소프트웨어 시스템의 여러 부분에 걸쳐 반복적으로 나타나는 공통된 기능 또는 문제

    - 로그 기록
    - 보안 강화
    - 트랜잭션 관리

```java
public void 구매내역조회() {
	로그인확인()
    구매내역 출력 로직()
}
```
```java
public void 구매하기() {
	로그인확인()
    구매하는 로직()
}
```
![](https://velog.velcdn.com/images/wxxhyeong/post/0f907c50-3239-45e6-abd5-eb4059d382fb/image.png)

이런 반복적인 기능을 **횡단 관심사(Cross-Cutting Concern)**라고 부릅니다. 횡단 관심사들은 특정 클래스나 메서드에 국한되지 않고, 시스템 전반에 걸쳐서 자주 나타나기 때문에 코드가 중복되고, 유지보수가 어려워질 수 있습니다.

예를 들어, 모든 메서드에서 로깅을 해야 한다면, 각 메서드마다 로깅 코드를 넣어야 하는데, 이렇게 되면 코드가 지저분해지고 변경 사항이 있을 때 일일이 수정해야 하는 문제가 생깁니다.

### AOP (Aspect Oriented Programming)
**정의**
- 관점 지향 프로그래밍
- 횡단 관심사(Cross-Cutting Concenrs)를 하나의 모듈로 분리하여, 비즈니스 로직과 별도로 관리할 수 있게 해주는 프로그래밍 기법

<br>

**개념**
- **Advice** :
    - 어드바이스는 애스펙트가 조인 포인트에서 수행할 작업을 정의합니다. 어드바이스는 메서드 호출 전후 또는 예외 발생 시점 등에서 동작할 수 있습니다.

    - Before
    - After return
    - Around
    - After

- **Join Point** :
    - 조인포인트는 어드바이스(Advice)가 실제로 실행될 수 있는 특정 시점이나 지점을 의미합니다.
      일반적으로 메서드 호출, 생성자 호출, 필드 접근 등이 조인포인트가 될 수 있습니다.

- **Pointcut** :
    - 포인트컷은 어떤 조인포인트에 어드바이스를 적용할지를 정의합니다.
      쉽게 말해, **포인트컷은 조인포인트를 필터링하는 역할**을 합니다. 수많은 조인포인트 중 서 특정 조건을 만족하는 조인포인트에만 어드바이스를 적용하도록 해줍니다.

- ** Aspect ** :

    - 애플리케이션의 여러 모듈에서 공통적으로 사용된 기능을 모듈화한 것
      예를 들어, Logging, Transaction 관리 같은 기능이 Aspect로 구현될 수 있다.

- **Target**
    - Advice를 삽입할 대상이 되는 객체
        - 비즈니스 로직을 수행하는 클래스가 될 수도 있고, 프록시 객체가 될 수도 있음
    - Pointcut으로 결정
- **Weaving**
    - AOP에서 Joinpoint들을 Advice로 감싸는 과정
    - 작성한 Advice(공통 코드)를 핵심 로직 코드에 삽입하는 것으로 적용 시점에 따라 3가지로 나뉨
        - 컴파일 시 위빙, 클래스 로딩 시 위빙, 런타임 시 위빙
- **AOP 프록시**
    - AOP 기능을 구현하기 위해 만든 프록시 객체
    - 스프링에서 AOP 프록시는 `JDK 동적 프록시`와 `CGLIB 프록시`가 있고, CGLIB 프록시가 기본값

![](https://velog.velcdn.com/images/wxxhyeong/post/ad04a6f6-184f-4c9e-9d34-7440fe8b5367/image.png)

### Advice
스프링에서는 Advice에 관련된 5가지 애노테이션을 제공하고, 포인트컷에 지정된 대상 메서드에서 Advice가 실행되는 시점을 정할 수 있음

- **Before Advice(@Before)**

    - 대상 객체의 메소드 호출 전에 공통 기능을 실행함
- **Around Advice(@Around)**

    - 대상 객체의 메소드 실행 전, 후 또는 익셉션 발생 시점에 공통 기능을 실행하는데 사용됨

    - 다양한 시점에 원하는 기능을 삽입할 수 있기 때문에 Advice 중 가장 널리 사용됨
- **After Advice(@After)**

    - 익셉션 발생 여부에 상관없이 대상 객체의 메소드 실행 후 공통 기능을 실행함

    - try/catch/finally의 finally 블록과 비슷함
- **After Returning Advice(@AfterRunning)**

    - 대상 객체의 메소드가 익셉션 없이 정상적으로 실행된 이후에 공통 기능을 실행함

- **After Throwing Advice(@AfterThrowning)**

    - 대상 객체의 메소드를 실행하는 도중 익셉션이 발생한 경우에 공통 기능을 실행함



```java
@Aspect
public class LoggingAspect {

    // 1. 포인트컷 정의: 서비스 패키지 내 모든 메서드
    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    // 2. 어드바이스 정의: 조인포인트에 적용될 로직
    @Before("serviceMethods()")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Before method: " + joinPoint.getSignature().getName());
    }

    @After("serviceMethods()")
    public void logAfter(JoinPoint joinPoint) {
        System.out.println("After method: " + joinPoint.getSignature().getName());
    }
}
```

**코드 실행 예시**:
- 만약 **com.example.service.OrderService** 클래스의 ```placeOrder()``` 메서드가 호출되면, 이 메서드는 ```"execution(* com.example.service.*.*(..))"``` 조건을 만족하므로 조인포인트가 됩니다.
- 따라서, ```placeOrder()``` 메서드가 실행되기 전에 ```logBefore``` 어드바이스가, 실행된 후에 ```logAfter``` 어드바이스가 적용됩니다.

```java
public class OrderService {
    public void placeOrder() {
        System.out.println("Placing order");
    }
}
```
```plaintext
Before method: placeOrder
Placing order
After method: placeOrder
```

- 조인포인트(Joinpoint): 어드바이스가 적용될 수 있는 실제 실행 지점(예: 메서드 호출).
- 포인트컷(Pointcut): 어떤 조인포인트에 어드바이스를 적용할지 결정하는 규칙.

### 동작원리 - 프록시 패턴
AOP는 프록시 패턴을 이용해 동작합니다. 프록시 패턴에서는 프록시 객체가 타겟 객체를 감싸고, 클라이언트가 프록시를 통해 타겟 객체에 접근하게 됩니다.

예를 들어, 클라이언트가 메서드를 호출하면 프록시 객체가 이 호출을 가로챕니다. 그런 다음, 프록시 객체는 미리 정의된 어드바이스(예: 로깅 기능)를 실행하고, 그 후에 타겟 객체의 실제 메서드를 호출하게 됩니다.

이런 방식으로, 우리는 비즈니스 로직을 수정하지 않고도 추가 기능을 쉽게 삽입할 수 있게 됩니다.

```java
public interface Service {
    void performAction();
}

public class RealService implements Service {
    @Override
    public void performAction() {
        System.out.println("Performing action in RealService");
    }
}

public class ServiceProxy implements Service {
    private RealService realService;

    public ServiceProxy(RealService realService) {
        this.realService = realService;
    }

    @Override
    public void performAction() {
        System.out.println("Logging before action");
        realService.performAction();  // 실제 객체의 메서드를 호출
        System.out.println("Logging after action");
    }
}
```
### AOP의 장점

**모듈화** : 횡단 관심사를 모듈화하여 코드 중복을 줄입니다.

**유지보수성** : 비즈니스 로직과 부가 기능이 분리되어 유지보수가 쉽습니다.

**관심사 분리** : 코드가 더 깔끔해지고 가독성이 좋아집니다.

### 결론
AOP는 비즈니스 로직과 부가 기능을 분리하여 애플리케이션의 **유지보수성**과 **확장성**을 크게 향상시키는 중요한 기법임을 강조하고 싶습니다.

AOP를 통해 우리는** 코드 중복을 줄이고**, **공통된 기능을 효율적으로 관리함**으로써 더 나은 소프트웨어를 개발할 수 있습니다.

@Aspect는 어떻게 동작하나요?