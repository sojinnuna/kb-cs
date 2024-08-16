## 11. IoC와 DI에 대해 설명해 주세요.
#### 제어의 역전 (Inversion Of Control)   
  - 객체의 생성과 의존성 주입을 외부에서 관리하는 개념  
  - 객체가 직접 다른 객체를 생성하거나 관리하는 행위를 외부로 위임하였다고 하여 역전이라는 표현을 사용   
  - 결과적으로 객체 간의 결합도를 낮추고 유연성과 확장성을 증가
#### 의존성 주입(Dependency Injection)
  - IOC를 구현한 방법
  - 객체의 의존성을 외부에서 주입
  - 객체가 자신의 의존성을 직접 생성하지 않음 
  - 생성자 기반 DI와 Setter 기반 DI가 있음
  - 생성자 기반 DI는 필수적인 의존성에서 사용
  - Setter 기반 DI는 선택적인 의존성에서 사용, 객체를 나중에 재주입, 재구성을 받을 수 있는 상태로 만듬
#### Spring에서의 IOC  
  - IOC Container, Spring Container, Application Context 라고 불림 (다 같은 의미)
  - IOC Container : 정의된 Bean들을 인스턴스화, 주입, 관리하는 책임이 있는 주체
  - Bean : IOC Contianer에 의해 인스턴스화, 주입, 관리되는 객체, Bean이 누구인지, 관계가 어떻게 되있는 지  정의되어 있음
  - Configuration Metadata에 Bean으로 관리될 객체가 누군지, 관계가 어떻게 되어있는지 기술되어 있음
  - XML, Annotation, Java를 기반으로 Configuration Metadata를 정의할 수 있음
  ![](https://docs.spring.io/spring-framework/docs/5.3.37/reference/html/images/container-magic.png)
### 후보 없이 특정 기능을 하는 클래스가 딱 한 개하면, 구체 클래스를 그냥 사용해도 되지 않나요? 그럼에도 불구하고 왜 Spring에선 Bean을 사용 할까요?
- 유연성, 확장성에 유리  
  특정 기능에 변경 사항이 생기더라도, 구체 클래스를 직접 수정하지 않고도 새로운 기능을 추가하거나 다른 구현체로 교체할 수 있습니다. 이는 OCP(개방-폐쇄 원칙)를 따르도록 도와주며, 코드가 수정에 대해서는 닫혀 있고, 확장에 대해서는 열려 있도록 만듭니다.
- 테스트 용이   
  테스트 시 의존성 주입을 통해 모의 객체(mock)를 사용하거나 특정 환경에 맞는 Bean을 대체할 수 있어 테스트가 용이합니다.
### Spring의 Bean 생성 주기에 대해 설명해 주세요.
- bean의 scope을 정의하여 생명 주기와 사용 범위를 정할 수 있습니다
- 기본적으로 singleton scope, prototype scope을 지원합니다
  1. singleton(default)   
    Application Context가 로드될 떄 인스턴스화 됩니다.
    컨테이너에서 빈을 하나만 생성, 사용하는 객체는 해당 객체의 동일한 인스턴스를 공유합니다.
    Application Context가 종료될 때 소멸됩니다.

  2. prototype   
    요청(DI)마다 빈의 새로운 인스턴스를 생성합니다.   
    컨테이너가 빈의 생성 주기 모두를 관리하지 않습니다.(클라이언트가 빈의 소멸을 정의하여 자원을 반환받아야 함)   

- Web 관련 Scope(Web Application Context에서 사용 가능)
  각 항목의 범위만큼 유지되는 스코프입니다.
  1. request
   HTTP 요청마다 하나의 빈을 생성, 요청이 끝나면 소멸됩니다
  2. session
   HTTP 세션마다 하나의 빈을 생성, 세션이 종료되면 소멸됩니다.
  3. application
   ServletContext 마다 하나의 빈을 생성합니다.
  4. websocket
   Websocket 마다 하나의 빈을 생성합니다.

### 프로토타입 빈은 무엇인가요?
- scope이 prototype으로 정의된 빈입니다.
- 요청(DI)마다 빈의 새로운 인스턴스를 생성합니다.
- 컨테이너가 빈의 생명주기를 모두 관리하지 않습니다.
- stateful baen에 대해 prototype bean을 사용하고 stateless bean에 대해 singleton bean을 사용합니다.
- singleton bean에 주입된 prototype bean
  singleton bean이 인스턴스화 되는 것은 한번(Application Context load)이고 그때 prototype bean이 주입됩니다. singleton bean에 의존하는 prototype bean은 새로운 호출에 대해 빈을 생성하지 않습니다. 만약 prototype bean이 상태를 가지고 있다면 해당 상태가 여러 클라이언트에서 공유될 수 있습니다(동시성 문제).   
  이런 문제를 해결하기 위해 런타임에 prototype bean을 생성하는 방법을 따라야합니다. 
### method injection
  sigleton bean에 주입되는 prototype bean을 메소드를 사용하여 주입합니다. XML, Annotation를 선택하여 구현할 수 있습니다. Annotation 방법은 @Lookup을 사용합니다. 컨테이너는 해당 Annotation이 붙은 메소드를 재정의(Override)하여 새로운 빈이 주입될 수 있게 합니다. 이런 방식을 구현하기 위해 Spring은 CGLIB 라이브러리를 사용하여 바이트코드에서 method injection을 구현합니다.
