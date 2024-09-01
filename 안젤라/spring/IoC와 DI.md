# **1. 객체지향 설계와 스프링**

- 스프링을 사용하기 전
  - 순수하게 자바로 OCP, DIP 원칙을 지키면서 개발해보면, 결국 스프링 프레임워크를 만들게 됨(정확히는 DI 컨테이너)
- 스프링은 `다형성`을 극대화해서 역할과 구현을 편리하게 다룰 수 있도록 지원함
  - 의존관계 주입(`DI`), `DI 컨테이너` 기술로 `OCP`와 `DIP`를 가능하게 지원함
  - 클라이언트 코드의 변경 없이 기능 확장 가능

## **1) 스프링 컨테이너와 스프링 빈**

```java
Spring Container: 스프링에서 자바 객체들을 관리하는 공간
Spring Bean: 스프링에 의해 생성되고 관리되는 자바 객체
```

### **스프링 컨테이너**

!https://private-user-images.githubusercontent.com/74135929/342657293-3957a3a9-13f0-49e9-8dfc-c417a1fe887b.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjQ3NDU3MjIsIm5iZiI6MTcyNDc0NTQyMiwicGF0aCI6Ii83NDEzNTkyOS8zNDI2NTcyOTMtMzk1N2EzYTktMTNmMC00OWU5LThkZmMtYzQxN2ExZmU4ODdiLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MjclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODI3VDA3NTcwMlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTY4ZDhiOTYwMzAxZjZlNGM4NDQyNTg3OWMyZWVlNDM1ODM4MWEzZjkwN2ZhN2U5OTk1MTQ0N2NlMDEzOThkNTImWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.zhuEuQyfeVzd8R2QeXkROFN4AvsrR7n9VS8jgJtsGXo

- 스프링 컨테이너 내부에는 스프링 Bean객체를 key-value형식으로 저장할수 있는 공간이 존재합니다.
- 해당 공간에 빈이름(key)과 빈 객체(value)를 등록해 DI와 IoC에 사용되게 됩니다.

### **빈 등록**

- @Configuration이 붙은 클래스가 빈 등록을 위한 설정파일이 되어, 클래스 내의 @Bean이 붙은 메서드가 스프링 빈이 되어 등록됩니다.
- **등록방법**
  - 스프링 프로젝트를 생성하면 기본적으로 Application클래스가 존재합니다.
  - 클래스에는 `@SpringBootApplication`가 붙어있고, 해당 어노테이션의 메타어노테이션으로 `@ComponentScan`이 붙어 `@Component`를 자동으로 컨테이너에 등록해줍니다.
  > @ComponentScan의 경우 @Component가 붙은 객체를 자동으로 스프링 빈으로 등록해주는 역할을 합니다.
  하지만 @Configuration이 붙는 설정파일의 경우 @Component가 없는데 어떻게 등록이 될까요?
  @Configuration의 메타데이터에 @Component가 붙어 자동 등록됩니다.

!https://private-user-images.githubusercontent.com/74135929/342673689-4904ad0b-f177-4e1b-ab92-9ac965cf4ddc.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjQ3NDU3MjIsIm5iZiI6MTcyNDc0NTQyMiwicGF0aCI6Ii83NDEzNTkyOS8zNDI2NzM2ODktNDkwNGFkMGItZjE3Ny00ZTFiLWFiOTItOWFjOTY1Y2Y0ZGRjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MjclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODI3VDA3NTcwMlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTk5MDAyOTViNGVlZWRmYzY3MDlkNDVjZWY1MzUxNzM4OWZmZjhhYzRlZGMxYmYyOTI1ZDgyMTg5ZDRhOGEyOTkmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.mkvHAtBCSqFME6ItQPTdO3qiv2rkzsI9THjxixIfDlA

### **빈 생명주기(Bean LifeCycle)**

!https://private-user-images.githubusercontent.com/74135929/342973580-3bd9e0c9-2efe-4e08-a39f-6859584b2cdd.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjQ3NDU3MjIsIm5iZiI6MTcyNDc0NTQyMiwicGF0aCI6Ii83NDEzNTkyOS8zNDI5NzM1ODAtM2JkOWUwYzktMmVmZS00ZTA4LWEzOWYtNjg1OTU4NGIyY2RkLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MjclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODI3VDA3NTcwMlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWNkN2NjZmUwMjI0OGUyYmQ1NTk5NTViNTgyNjg4ZWY0NTMwZjBkNjIxMTMyMDAzNDQ0NzNiOWE0NWU3OTM4OGQmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.cuHfTNpKHl3uJ4jnWx3SxaDo7VHqsRtgqNJY9HMhhU8

- 스프링 빈 이벤트 라이프 사이클
  - `스프링 IoC 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → 초기화 콜백 메소드 호출 → 사용 → 소멸 전 콜백 메소드 호출 → 스프링 종료`

## **2) 스프링 빈 스코프**

- 빈 스코프는 빈이 존재할 수 있는 범위를 뜻함
  - 스프링은 다양한 스코프를 지원함
- 싱글톤(Singleton) : 기본 스코프, 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프이다.
- 프로토타입(Prototype) : 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프이다.
- 웹 관련 스코프
  - request : 웹 요청이 들어오고 나갈 때까지 유지되는 스코프
  - session : 웹 세션이 생성되고 종료될 때까지 유지되는 스코프
  - application : 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프

### **싱글톤**

!https://private-user-images.githubusercontent.com/74135929/342708664-c1d3f053-dad1-4c07-8195-2b59cc4ef1cc.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjQ3NDU3MjIsIm5iZiI6MTcyNDc0NTQyMiwicGF0aCI6Ii83NDEzNTkyOS8zNDI3MDg2NjQtYzFkM2YwNTMtZGFkMS00YzA3LTgxOTUtMmI1OWNjNGVmMWNjLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MjclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODI3VDA3NTcwMlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTc4OGIwMzY0YWRhMGUzYTkxZTYxNjllYTIwYWNmYzNjZmUzNzBmOTE1ZDE4ZTMxNDgxNTRkNGEyZTMzZTNmNmImWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.5w9K6b_wCzIuyPcpcmEzMhzYD2grPqW9gTlJ63oB23w

- 싱글톤 스코프의 빈을 조회하면 스프링 컨테이너는 항상 같은 인스턴스의 빈을 반환한다.

### **프로토 타입**

!https://private-user-images.githubusercontent.com/74135929/342708932-9455ab05-245f-4dc8-ba34-ab9ce016ef45.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjQ3NDU3MjIsIm5iZiI6MTcyNDc0NTQyMiwicGF0aCI6Ii83NDEzNTkyOS8zNDI3MDg5MzItOTQ1NWFiMDUtMjQ1Zi00ZGM4LWJhMzQtYWI5Y2UwMTZlZjQ1LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MjclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODI3VDA3NTcwMlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTJiNTE2OTY0MTQxNzc0NjM1ZjJkN2MwNzE5NTM3OGM2ODJkOGE4ZDUxY2YyYjQ4MmE4YTQ5NzI5M2I2YWIyZjQmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.T8Z_cW4YVpitdNFya5WsO4N7iDc7G4BqEsotq2zfi-c

- 클라이언트가 프로토타입의 빈을 조회요청
- 스프링 컨테이너는 요청시점에 프로토타입 빈을 생성하고, 의존관계 주입
- 생성된 프로토타입 빈을 반환

## **3) 웹 스코프**

- 웹 환경에서만 동작하는 스코프
- 스프링이 해당 스코프의 종료시점까지 관리하며, 종료 메서드도 호출된다.

> request : HTTP요청이 들어오고 나갈때까지 유지되는 스코프로 각각의 요청마다 별도의 빈 인스턴스가 생성 및 관리된다.
> session : HTTP Session과 동일한 생명주기를 가진다
> application : 서블릿 컨텍스트(ServletContext)와 같은 생명주기를 가지는 스코프
> websocket : 웹소켓과 동일한 생명주기를 가지는 스코프

# **2. DI (Dependency Injection)**

- 애플리케이션 **실행 시점(런타임)** 에 **스프링컨테이너**에서 객체를 생성하여 의존관계가 맺는 과정
  - 객체 인스턴스를 생성하고, 그 참조 값을 전달해서 연결하는 과정
- 만약 DI가 없다면 직접 의존하고 싶은 클래스의 인스턴스를 생성하여 사용해야 합니다.
  - Basketball를 다른 클래스로 교체하고 싶다면 GroundService의 코드를 수정해야 한다는 문제점이 생깁니다.
    따라서 스프링은 애플리케이션 실행시점에 스프링 컨텍스트에 객체(빈)를 싱글톤 패턴으로 미리 생성하여 필요에 따라 하나의 객체만 생성하여 주입해주는 DI를 3가지 방식으로 지원해줍니다

```java
//Sport는 인터페이스, Basketball은 Sport의 구현체이다.

public class GroundService{
	private final Sport sport = new Basketball();
}
```

> 참고 : @Autowired는 스프링 컨텍스트에서 해당되는 타입의 빈을 찾아 주입해주는 어노테이션

## **스프링의 DI 방식**

- Setter Injection (Setter 주입)
- Field Injection (필드 주입)
- Contruct Injection (생성자 주입)

### **Setter Injection (Setter 주입)**

```java
public class GroundService{
	private Sport sport;
    @Autowired
    public void setSport(Sport sport){
    	this.sport = sport;
    }
}
```

- set메서드를 사용하여 메서드를 public으로 열어둬야하기 때문에 의존성이 어디서든 변경될 가능성이 있습니다.
- 물론 의존성을 변경해야하는 상황도 존재하지만 거의 존재하지 않기때문에 지양합니다.

### **Field Injection (필드 주입)**

```java
public class GroundService{
@Autowired
private Sport sport;
}
```

- 필드 주입을 사용하면 IDE상에서 Field injection is not recommended라는 에러가 뜨는것처럼 사용을 지양하고 있습니다.
- 필드에 @Autowired만 붙히면 자동으로 의존성이 주입되어 깔끔하다는 장점이 있지만 의존성에 접근이 불가하다는 단점이 있습니다.

### **Contruct Injection (생성자 주입)**

```java
public class GroundService{
    private final Sport sport;
    @Autowired
	public GroundService(Sport sport){
		this.sport = sport;
	}
    public void play(){
    }
}
```

- 스프링에서 가장 권장하는 방법입니다. 생성자 주입의 장점은 다음과 같습니다.
  1. 객체의 불변성
     - 생성자를 final로 선언할 수 있고, 이로 인해 setter주입과 같이 런타임 시점에 의존성 주입받는 객체가 변경될 일이 없어집니다(객체의 불변성).
- **개발자가 직접 주입할 객체를 지정한 것도 아닌데, 해당 객체를 어떻게 판단하여 주입할 수 있는것일까요?**
  비밀은 스프링의 IoC에 숨겨져 있습니다.!

# **3. IOC (Inversion of Control)**

!https://private-user-images.githubusercontent.com/74135929/342644991-8306a9c1-e92d-44f2-9026-202a9e918b88.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MjQ3NDU3MjIsIm5iZiI6MTcyNDc0NTQyMiwicGF0aCI6Ii83NDEzNTkyOS8zNDI2NDQ5OTEtODMwNmE5YzEtZTkyZC00NGYyLTkwMjYtMjAyYTllOTE4Yjg4LnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA4MjclMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwODI3VDA3NTcwMlomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTVhMzkyMDhkNjg0N2QxMmE1NGY2YmI2MzY3YTJhMGM0ODVlZmM1OTljNmMzZGE2Mzk2ZmQ4MDQyNjJhNmM0MTYmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.Iy0iXq_hE5wJeWq-B9Whzev3WmnLsldk3JNc0LrPzvI

- **xml파일, 어노테이션(@Bean)으로 스프링 컨테이너에 객체를 등록하면 스프링 컨테이너에서 객체의 생명주기를 관리**
- 즉, 객체의 제어가 컨테이너(스프링)으로 바뀌기 때문에 제어의 역전(IoC)라 부름
  > IoC가 없다면 개발자가 직접 객체를 제어해야함.
  >
  > - 객체 생성, 의존성 설정 등등

## **스프링 빈을 스프링 컨테이너에서 꺼내오는 과정**

- BeanFactory 스프링은 BeanFactory 클래스를 통해 빈을 관리하게 됩니다.

```java
public interface BeanFactory {
	...
	Object getBean(String name) throws BeansException;
	...
}
```

- 해당 클래스를 살펴보면 DI 해야하는 객체를 인스턴스화 할수 있도록 getBean() 메서드를 지원하는 것을 볼 수 있습니다.

### ApplicationContext 그렇다면 프로젝트의 어디에서 BeanFactory를 사용하게 될까요?

- 프로젝트를 생성하면 기본으로 존재하는 `(프로젝트명)Application` 클래스의 `SpringApplication.run((프로젝트명)Application.class, args);` 안에 중요한 부분이 포함되어 있습니다.
- `run` 메서드를 살펴보면 `ConfigurableApplicationContext` 클래스가 사용되는 것을 알 수 있습니다.
- `ConfigurableApplicationContext`는 여러 인터페이스를 상속받고 있으며, 그중에서 `BeanFactory`는 가장 기초적인 기능을 제공하는 인터페이스로, Spring의 **빈(Bean) 관리**를 담당합니다. `ApplicationContext`는 이 `BeanFactory`를 상속받아 추가적인 부가 기능을 제공합니다.
- `ApplicationContext`는 `BeanFactory`를 상속받아서 `BeanFactory`의 기본 기능 외에도 다양한 부가 기능을 제공하는 인터페이스로, **Spring 애플리케이션 구동 시점**에 등록된 Bean 객체들을 스캔하고, 이를 관리합니다. 실제 개발에서는 `ApplicationContext`를 통해 Bean을 주로 관리하고 사용합니다.
