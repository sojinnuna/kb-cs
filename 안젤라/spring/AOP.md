## 면접 질문

**12. AOP에 대해 설명해 주세요.**

- @Aspect는 어떻게 동작하나요?
    
    `@Aspect`는 Spring AOP에서 사용되는 어노테이션으로, 특정 클래스가 Aspect로 동작할 수 있도록 설정합니다. Spring AOP는 프록시 기반의 AOP를 사용하기 때문에, 대상 객체에 프록시를 생성하여 Advice를 삽입합니다.
    

## 1. AOP

### **💡 Spring AOP란?**

- **Spring AOP는 스프링 프레임워크에서 제공하는 기능 중 하나로 관점 지향 프로그래밍을 지원하는 기술입니다.** Spring AOP는 로깅, 보안, 트랜잭션 관리 등과 같은 공통적인 관심사를 모듈화 하여 코드 중복을 줄이고 유지 보수성을 향상하는데 도움을 줍니다.

### **💡 관점 지향 프로그래밍(Aspect-Oriented Programming, AOP) 이란?**

- 객체 지향 프로그래밍 패러다임을 보완하는 기술로 **메소드나 객체의 기능을 핵심 관심사(Core Concern)와 공통 관심사(Cross-cutting Concern)로 나누어 프로그래밍하는 것**을 말합니다. **“핵심 관심사”는 각 객체가 가져야 할 본래의 기능**이며,**“공통 관심사”는 여러 객체에서 공통적으로 사용되는 코드**를 말합니다.
- **여러 개의 클래스에서 반복해서 사용하는 코드가 있다면 해당 코드를 모듈화 하여 공통 관심사로 분리합니다.** 이렇게 분리한 공통 관심사를 Aspect로 정의하고 Aspect를 적용할 메소드나 클래스에 Advice를 적용하여 공통 관심사와 핵심 관심사를 분리할 수 있습니다. 이렇게 AOP에서는 공통 관심사를 별도의 모듈로 분리하여 관리하며, 이를 통해 코드의 재사용성과 유지 보수성을 높일 수 있습니다.

## 2. AOP 상세히 알아보기

---

!https://blog.kakaocdn.net/dn/bzOe1T/btr1udp1dDF/dAhGHfu6Ya9M97pM3SLndk/img.png

> 💡 관점 지향 프로그래밍의 정의를 보면 핵심 관심사와 공통 관심사를 분리하여 프로그래밍하는 것을 의미합니다.
> 
> 
> 💡 여기서 3개의 A, B, C의 클래스가 있다고 가정합니다.
> 
> 클래스 A에서는 주황, 파랑, 빨간색 블록으로 구성이 되어 있고 클래스 B에서는 빨강, 주황 블록으로 구성되어 있으며 클래스 C에서는 주황, 파랑 블록으로 구성되어 있습니다. **해당 색은 클래스 A, B, C에서 동일하게 사용되는 코드를 의미합니다.**
> 
> 예를 들어서 클래스 A에서 주황색 블록을 수정을 하게 되면 클래스 B, C에서도 수정을 해야 합니다. 이렇게 되면 유지보수 차원에서 모든 코드를 수정해야 하니 불편한 점이 있습니다. 그래서 Aspect X에서는 공통 관심사인 주황색 블록을 묶어서 모듈화를 시켜서 코드의 재 사용성과 유지 보수성을 강화하였습니다.
> 
> **이렇듯, 관점 지향 프로그래밍에서는 소스코드에서 반복적으로 사용하는 코드를 하나로 묶어서 모듈화하여 재사용성과 유지 보수성을 높일 수 있는 강점을 가지고 있습니다.**
> 

### 1. 주요 용어

---

| **용어** | **설명** |
| --- | --- |
| **Aspect** | - 공통적인 기능들을 모듈화 한것을 의미합니다. |
| **Target** | - Aspect가 적용될 대상을 의미하며 메소드, 클래스 등이 이에 해당 됩니다. |
| **Join point** | - Aspect가 적용될 수 있는 시점을 의미하며 메소드 실행 전, 후 등이 될 수 있습니다. |
| **Advice** | - Aspect의 기능을 정의한 것으로 메서드의 실행 전, 후, 예외 처리 발생 시 실행되는 코드를 의미합니다. |
| **Point cut** | - Advice를 적용할 메소드의 범위를 지정하는 것을 의미합니다. |

### **2. 주요 어노테이션**

---

| **메서드** | **설명** |
| --- | --- |
| **@Aspect** | 해당 클래스를 Aspect로 사용하겠다는 것을 명시합니다. |
| **@Before** | 대상 “메서드”가 실행되기 전에 Advice를 실행합니다. |
| **@AfterReturning** | 대상 “메서드”가 정상적으로 실행되고 반환된 후에 Advice를 실행합니다. |
| **@AfterThrowing** | 대상 “메서드에서 예외가 발생”했을 때 Advice를 실행합니다. |
| **@After** | 대상 “메서드”가 실행된 후에 Advice를 실행합니다. |
| **@Around** | 대상 “메서드” 실행 전, 후 또는 예외 발생 시에 Advice를 실행합니다. |

```java
package com.adjh.multiflexapi.aspects;

import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

/**
 * 로깅 관련 AOP 구성
 *
 * @author : jonghoon
 * @fileName : LoggingAspect
 * @since : 2023/03/01
 */
@Aspect
@Component
@Slf4j
public class LoggingAspect {

    /**
     * Before: 대상 “메서드”가 실행되기 전에 Advice를 실행합니다.
     *
     * @param joinPoint
     */
    @Before("execution(* com.adjh.multiflexapi.controller.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        log.info("Before: " + joinPoint.getSignature().getName());
    }

    /**
     * After : 대상 “메서드”가 실행된 후에 Advice를 실행합니다.
     *
     * @param joinPoint
     */
    @After("execution(* com.adjh.multiflexapi.controller.*.*(..))")
    public void logAfter(JoinPoint joinPoint) {
        log.info("After: " + joinPoint.getSignature().getName());
    }

    /**
     * AfterReturning: 대상 “메서드”가 정상적으로 실행되고 반환된 후에 Advice를 실행합니다.
     *
     * @param joinPoint
     * @param result
     */
    @AfterReturning(pointcut = "execution(* com.adjh.multiflexapi.controller.*.*(..))", returning = "result")
    public void logAfterReturning(JoinPoint joinPoint, Object result) {
        log.info("AfterReturning: " + joinPoint.getSignature().getName() + " result: " + result);
    }

    /**
     * AfterThrowing: 대상 “메서드에서 예외가 발생”했을 때 Advice를 실행합니다.
     *
     * @param joinPoint
     * @param e
     */
    @AfterThrowing(pointcut = "execution(* com.adjh.multiflexapi.controller.*.*(..))", throwing = "e")
    public void logAfterThrowing(JoinPoint joinPoint, Throwable e) {
        log.info("AfterThrowing: " + joinPoint.getSignature().getName() + " exception: " + e.getMessage());
    }

    /**
     * Around : 대상 “메서드” 실행 전, 후 또는 예외 발생 시에 Advice를 실행합니다.
     *
     * @param joinPoint
     * @return
     * @throws Throwable
     */
    @Around("execution(* com.adjh.multiflexapi.controller.*.*(..))")
    public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
        log.info("Around before: " + joinPoint.getSignature().getName());
        Object result = joinPoint.proceed();
        log.info("Around after: " + joinPoint.getSignature().getName());
        return result;
    }
```

### **3. 다양한 AOP 적용 방법**

- 컴파일

⇒ [A.java](http://a.java/) —-(AOP) —→ A.class(AspectJ)

- **바이트코드 조작**

⇒  [A.java](http://a.java/) → A.class —-(AOP)—→ 메모리(AspectJ)

프록시패턴과는 다르게 바이트코드 조작방식은 타깃 오브젝트를 뜯어고쳐서 부가기능을 직접 넣어주는 직접적인 방법을 택한다.

AspectJ 는 프록시와는 다르게 좀 더 직접적인 방법으로 부가기능을 제공하는데, 컴파일된 Target의 클래스 파일 자체를 수정하거나 클래스가 JVM에 로딩되는 시점을 가로채 바이트코드를 조작하는 방법을 사용한다. 그렇기 때문에 .java파일과 .class 파일을 비교해보면 내용이 달라진걸 확인할 수 있다. 

**해당 방법(바이트코드 조작)을 사용하는 이유는 두가지가 있다.** 

1. 스프링과 같은 DI컨테이너의 도움을 받지 않아도 AOP를 적용할 수 있기 때문이다. 그렇기에 스프링과같은 컨테이너가 사용되지 않는 환경에서도 손쉽게 AOP의 적용이 가능해진다.
2. 프록시 방식보다 강력하고 유연한 AOP가 가능하다. 
프록시를 AOP의 핵심 메커니즘으로 사용할 경우 부가기능(공통 모듈)을 부여할 대상은 클라이언트가 호출할 때 사용하는 메소드로 제한된다.
하지만, 바이트코드 조작 방식을 사용하면, 오브젝트의 생성, 필드 값 조회및 조작, 스태틱 초기화 등 다양한 작업에 부가기능을 부여할 수 있다.
이처럼 프록시를 사용한 AOP에서는 불가능한 부분에서까지 부가기능 부여가 가능하기 때문에 강력하고 유연하다.

- **프록시 패턴**(**스프링 AOP가 사용하는 방법**)

⇒ 공통 모듈을 프록시로 만들어서 DI 로 연결된 빈 사이에 적용해 Target의 메소드 호출 과정에 참여애 부가기능(공통 모듈)을 제공해준다.

그렇기의 JDK 와 Spring Container 외에 특별한 기술 및환경을 요구하지 않는다.

Advice 가 구현하는 MethodInterceptor 인터페이스는 다이내믹 프록시의 InvocationHandler와 마찬가지로 프록시부터 메소드 요청정보를 전달받아 타깃 오브젝트의 메소드를 호출하는데, 이렇게 메소드를 호출하는 전/후로 부가기능(공통 모듈)을 제공할 수 있다.

이런식으로 독립적으로 개발한 부가기능 모듈을 다양한 타깃 오브젝트의 메소드에 다이내믹하게 적용해주기 위해 가장 중요한 역할을 맡고 있는게 프록시고, 스프링 AOP는 프록시 방식의 AOP라 할 수 있다.

## **3. AOP 적용 전/후 의존관계**

https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F839a65b1-d90b-4e4e-92b0-41411760caf6%2F_2020-08-06__8.31.29.png&blockId=8b231581-3a78-485a-97c5-f3639f05a58b

**AOP 적용 전 의존관계**

- helloController는 memberService의 기능들을 사용하며 의존관계가 수립됩니다.
- 컨트롤러에서는 memberService의 실제 객체(Real Subject)에 바로 접근하여 로직을 수행시킵니다.
- memberService 에서는 공통적인 기능들을 추가하려면 각각의 메서드마다 기능들을 추가/수정/삭제 해야합니다.

https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb3a29e6a-cfd9-4bf1-be49-55206c4c1af5%2F_2020-08-06__8.31.48.png&blockId=95519934-219a-4d02-85fe-deeaf3c3e6a8

**AOP 적용 후 의존관계**

- helloController와 memberService 간에 프록시 객체인 memberServiceProxy가 추가되었습니다.
- helloController 는 이제  실제객체(Real Subject)에 바로 접근하지않고 프록시객체를 거쳐 프록시 객체에서 실제 객체에 접근하여 로직을 수행합니다.

https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe2c9c90b-bdbb-43ae-ac00-48c65bb2a144%2F_2020-08-06__8.32.01.png&blockId=749c3bbe-6c62-4cb2-923a-4e18fcb45ca3

**AOP 적용 전 전체 그림**

- Client가 Request를 하였을 때 스프링 컨테이너 내부의 의존관계를 보여줍니다.
- helloController 가 memberService 에 메서드를 호출해 비즈니스 로직을 수행하고 memberService에서는 memberRepository 를 호출하여 레파지토리에서는 DB에 접근해 데이터를 조회/수정/삭제 합니다.
- 모두 실제객체에 바로바로 접근합니다.

https://oopy.lazyrockets.com/api/v2/notion/image?src=https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbaff68c9-468e-41f0-99a8-da038b8d3d40%2F_2020-08-06__8.32.12.png&blockId=bf4912e1-965d-488d-9671-a4d21019a087

- 모든 객체들에게 프록시객체가 생성되었고 의존관계도 프록시 객체를 통하여 이뤄집니다.
- 각각 객체의 기능들을 수행하려 메서드를 호출하면 요청을 프록시 객체가 전달받아서 전처리/후처리 등 추가적인 작업을 수행하면서 실제 객체(Real Subject)에 로직을 수행합니다.
