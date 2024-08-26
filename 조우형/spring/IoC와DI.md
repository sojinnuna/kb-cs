# 스프링 빈
![](https://velog.velcdn.com/images/wxxhyeong/post/e36d1ed4-7e21-46f9-ab61-0c1c408d93c2/image.png)

### 스프링 빈(Spring Bean)이란?
- 스프링 빈은 스프링 컨테이너가 관리하는 객체
- 스프링 컨테이너에 의해 생명 주기가 관리되며, 의존성 주입 등의 기능을 사용할 수 있다.
- 스프링 Bean은 스프링에서 사용하는 **POJO 기반 객체**다.

> #### POJO (Plain Old Java Object) 기반 객체
POJO는 객체 지향적인 원리에 충실하면서 환경과 기술에 종속되지 않고, 필요에 따라 재활용될 수 있는 방식으로 설계된 오브젝트를 의미한다.
>> 1. Java나 Java의 스펙에 정의된 것 이외에는 다른 기술이나 규약에 얽매이지 않아야 한다.
>2. 특정 환경에 종속적이지 않아야 한다.

<br>
<br>

# 스프링 컨테이너
** 개념 **
- 스프링 컨테이너는 애플리케이션의 설정 정보를 바탕으로 스프링 빈을 생성하고, 의존 관계 주입, 생명 주기 관리 등을 수행하는 역할을 한다.

![](https://velog.velcdn.com/images/wxxhyeong/post/cca08051-3816-4cc0-a6c7-abc72c54da21/image.png)

![](https://velog.velcdn.com/images/wxxhyeong/post/66d468a4-d3b8-4366-8093-4c552a370623/image.png)

![](https://velog.velcdn.com/images/wxxhyeong/post/9861496d-4d2a-4c64-a9fa-f7e5d9505a94/image.png)


**빈 이름**
- 빈 이름은 메서드 이름을 사용한다.
- 빈 이름을 직접 부여할 수 도 있다.
  ```@Bean(name = "discountPolicy")```

> **주의: 빈 이름은 항상 다른 이름을 부여**해야 한다. 같은 이름을 부여하면, 다른 빈이 무시되거나, 기존 빈을 덮어버리거나 설정에 따라 오류가 발생한다.

<br>
<br>

# 스프링 빈 생명주기
**스프링 빈의 이벤트 라이프사이클**

```스프링 컨테이너 생성 --> 스프링 빈 생성 --> 의존관계 주입 --> 초기화 콜백 --> 사용 --> 소멸전 콜백 --> 스프링 종료```


> ** 객체의 생성과 초기화를 분리하자 **
생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다.
반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행한다.
따라서 객체를 생성하는 부분과 초기화하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋다.

### 빈 생명주기 콜백
** 개념 **
- 빈이 생성되거나 소멸될 때, 혹은 초기화 될 대 자동으로 실행되는 메서드

    - **초기화 콜백**: 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출
    - **소멸전 콜백**: 빈이 소멸되기 직전에 호출

### 빈 생명주기 콜백의 필요성
- 위에서 언급한 것처럼 스프링 빈에서 초기화 작업을 하고 싶다면 의존관계 주입이 다 이뤄진 후에 호출해야 한다.
- 스프링에서는 스프링 빈이 의존관계 주입이 완료되면 콜백 메서드를 통해 초기화 시점을 알려주는 기능을 제공한다.

- 스프링 컨테이너의 소멸 직전 소멸 콜백을 통해 스프링 컨테이너가 종료되기 전 로직을 수행할 수 있다.
- 객체가 적절히 생성되고 소멸될 수 있도록 도와주며, 메모리 누수나 리소스 낭비를 방지할 수 있다.

<br>

### 스프링 빈 생명주기 콜백 지원
- 인터페이스(InitializingBean, DisposalBean)
- 설정 정보에 초기화 메서드, 종료 메서드 지정
- @PostConstruct, @PreDestroy 어노테이션 지원


**초기화 콜백**: 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출
**소멸전 콜백**: 빈이 소멸되기 직전에 호출

### 빈 생명주기 콜백 사용 예시
- 리소스 초기화 (예: 데이터베이스 연결 설정)
- 리소스 정리 (예:  파일 닫기, 연결 종료)
  <br>
  <br>



# 스프링 빈 스코프
**개념**
- 빈 스코프는 빈이 생성되고 관리되는 범위를 정의한 것

Bean은 상황과 필요에 따라 사용할 때 하나만 만들어야 할 수도 있고, 여러개가 필요할 때도 있고, 어떤 한 시점에서만 사용해야할 때가 있을 수 있다.

이를 위해 Scope를 설정해서 Bean의 사용 범위를 개발자가 설정할 수 있다.


우선 따로 설정을 해주지 않으면, Spring에서 Bean은 **Singleton**으로 생성된다. 싱글톤 패턴처럼 특정 타입의 Bean을 딱 하나만 만들고 모두 공유해서 사용하기 위함이다. 보통은 Bean을 이렇게 하나만 만들어 사용하는 경우가 대부분이지만, 요구사항이나 구현에 따라 아닐 수도 있을 것이다.

따라서 Bean Scope는 싱글톤 말고도 여러가지를 지원해준다.

### 빈 스코프 종류
#### singleton
-  기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.
- 해당 Bean에 대해 IoC 컨테이너에서 단 하나의 객체로만 존재한다.

- 주로 상태를 공유하지 않는 서비스 객체에 사용된다.

#### prototype
- 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프이다
- 해당 Bean에 대해 다수의 객체가 존재할 수 있다.
- 상태를 가지는 객체나, 매번 새로 생성해야 하는 객체에 사용된다.

- 싱글톤 스코프의 빈을 조회하면 항상 같은 인스턴스의 스프링 빈을 반환한다. 하지만 프로토타입 스코프를 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성해서 반환한다.

![](https://velog.velcdn.com/images/wxxhyeong/post/bfb0ffd0-1542-4126-8d80-d9e200be81fc/image.png)

![](https://velog.velcdn.com/images/wxxhyeong/post/a15ea4b7-3a38-46b3-86a2-233b2b2ea99b/image.png)

**스프링 컨테이너는 프로토타입 빈을 생성하고, 의존관계 주입, 초기화까지만 처리한다는 것이 핵심이다.**


## 웹 스코프의 특징

- 웹 스코프는 웹 환경에서만 동작한다.
- 웹 스코프는 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리한다. 따라서 종료 메서드가 호출된
  다

## 웹 스코프의 종류

### request
-  HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다.
- 해당 Bean에 대해 하나의 HTTP Request의 라이프사이클에서 단 하나의 객체로만 존재한다.

- 각 요청마다 별도의 상태를 유지해야 하는 객체에 사용된다.


### session
- 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프이다.
- HTTP Session과 동일한 생명주기를 가지는 스코프

- 해당 Bean에 대해 하나의 HTTP Session의 라이프사이클에서 단 하나의 객체로만 존재한다.

- 사용자 세션별로 상태를 유지해야 하는 객체에 사용된다.

### application
- 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프이다.

- 애플리케이션 전역에서 하나의 인스턴스만 존재해야 하는 객체에 사용됩니다.


request, session, application 은 MVC 웹 어플리케이션에서만 사용함

# DI, Dependency Injection (의존성 주입)
**개념**
- 스프링 컨테이너가 객체 간의 의존성을 관리하고 주입해주는 기능
- 객체 간의 의존 관계를 코드 내부에서 직접 설정하지 않고 외부에서 주입하는 디자인 패턴

**기존의 의존성 주입**  
[의존성 주입 실습 예제](https://velog.io/@wxxhyeong/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-1-%EC%98%88%EC%A0%9C-%EB%A7%8C%EB%93%A4%EA%B8%B0-2) 의 일부를 가져왔다.

```java
public interface DiscountPolicy {
}
```

```java
public class FixedDiscountPolicy implements DiscountPolicy {
// 1000원 할인해주는 정책
}
```

```java
public class RateDiscountPolicy implements DiscountPolicy {
// 10% 할인해주는 정책
}
```
```java
public class MemberService {
	private final DiscountPolicy discountPolicy = new FixedDiscountPolicy();
}
```
위 코드의 문제점은 SOLID 원칙 중 OCP와 DIP를 지키지 않았다.
1. **DIP** : ```MemberService```가 인터페이스가 아닌 구현 클래스인 ```FixedDiscountPolicy```에 의존하고 있다.
2. **OCP** : OCP는 변경하지 않고 확장할 수 있는 원칙이다. 즉 클라이언트 코드에서 변경은 없어야 하지만, 위 코드를 보면 ```discountPolicy```가 다른 정책을 적용하려면 ```MemberService```에서 ```FixedDiscountPolicy()```를 변경해야 한다.

그렇다면 해결 방법은?

```java
public class MemberService {
	private final DiscountPolicy discountPolicy;
    
    public MemberService(DiscountPolicy discountPolicy) {
    	
        this.discountPolicy = discountPolicy;
    }
```

```MemberService```에서 생성자로 넘어온 할인정책을 적용해주는 것을 볼 수 있다.
이로서 우리는 ** OCP ** 와 ** DIP **를 해결한 것처럼 보인다.

그렇다면 ```MemberService```의 생성자로 받아오는 ```discountPolicy```는 어디서 받아오는 것일까?

```java
public class AppConfig {

    public MemberService memberService(){
        return new MemberService(new FixedDiscountPolicy());
    }
```
[AppConfig 관련 설명](https://velog.io/@wxxhyeong/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC1-%EC%98%88%EC%A0%9C-%EB%A7%8C%EB%93%A4%EA%B8%B0-3)

AppConfig에 설정 정보들을 등록하면서 생성자로 구현 객체를 주입시켰다.
지금은 순수 자바 코드로 작성하였지만 스프링에서는 어노테이션으로 의존성 주입을 할 수 있다.

```java
@Service // MemberSerivce를 스프링 컨테이너에 스프링 빈으로 등록
public class MemberService {
	private final DiscountPolicy discountPolicy;

@Autowired
public MemberService(DiscountPolicy discountPolicy) {
	this.DiscountPolicy = discountPolicy;
```
# IoC (Inversion of Control)

- 기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성하고, 연결하고, 실행했다. 즉 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다.
- 반면에 AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다. 프로그램의 제어 흐름은 이제 AppConfig가 가져간다. 예를 들어서 OrderServiceImpl은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모른다.

- 프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있다. 심지어 OrderSerivceImpl도 AppConfig가 생성한다.
- 이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)라고 한다.
